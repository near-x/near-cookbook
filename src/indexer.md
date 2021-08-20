# Indexer

## Introduction

- [What is Indexer Framework](https://docs.near.org/docs/tools/near-indexer-framework)
- [Source Code](https://github.com/near/nearcore/tree/master/chain/indexer)

## Usage

### NEAR Indexer for Explorer

- [NEAR Indexer for Explorer](https://github.com/near/near-indexer-for-explorer)

#### Shared Public Access

NEAR runs the indexer and maintains it for NEAR Explorer, NEAR Wallet, and some other internal services. It proved to be a great source of data for various analysis and services, so we decided to give a shared read-only public access to the data:

- **testnet** credentials: `postgres://public_readonly:nearprotocol@35.184.214.98/testnet_explorer`
- **mainnet** credentials: `postgres://public_readonly:nearprotocol@104.199.89.51/mainnet_explorer`

#### Database Structure

![](https://github.com/near/near-indexer-for-explorer/raw/master/docs/near-indexer-for-explorer-db.png)


## Common SQL Recipes

**Total New Accounts By Date**

```SQL
SELECT
        TIMESTAMP ‘epoch’ + DIV(DIV(blocks.block_timestamp, 1000000000), 60 * 60 * 24) * INTERVAL ‘1 day’ AS “date”,
        COUNT(*) as new_accounts_count_by_date
FROM accounts
JOIN receipts ON receipts.receipt_id = accounts.created_by_receipt_id
JOIN blocks ON blocks.block_hash = receipts.included_in_block_hash
GROUP BY “date”
ORDER BY “date”
```

**Top 10 Accounts with most Transactions in Past Two Weeks**

```SQL
SELECT signer_account_id,
        COUNT(*) AS transactions_count
FROM transactions
WHERE transactions.block_timestamp >= (cast(EXTRACT(EPOCH FROM NOW()) - 60 * 60 * 24 * 14 AS bigint) * 1000 * 1000 * 1000)
AND transactions.block_timestamp < (cast(EXTRACT(EPOCH FROM NOW()) AS bigint) * 1000 * 1000 * 1000)
GROUP BY signer_account_id
ORDER BY transactions_count DESC
LIMIT 10
```


**Active Accounts by Date**

```SQL
SELECT
        TIMESTAMP 'epoch' + DIV(DIV(transactions.block_timestamp, 1000000000), 60 * 60 * 24) * INTERVAL '1 day' AS "date",
        COUNT(distinct transactions.signer_account_id) as active_accounts_count_by_date
      FROM transactions
      JOIN execution_outcomes ON execution_outcomes.receipt_id = transactions.converted_into_receipt_id
      WHERE execution_outcomes.status IN ('SUCCESS_VALUE', 'SUCCESS_RECEIPT_ID') and 
      TIMESTAMP 'epoch' + DIV(DIV(transactions.block_timestamp, 1000000000), 60 * 60 * 24) * INTERVAL '1 day' > current_date -7 
      GROUP BY "date"
      ORDER BY "date"
```


## Real-World Examples

### BerryClub Wayback

[BerryClub Wayback](https://wayback.berryclub.io/ ) used indexer to fetch 50 edits randomly from all the edits or by a user

- [indexer client source code](https://github.com/vgrichina/berry-wayback/blob/master/indexer.js)

**find edits**: [source code](https://github.com/vgrichina/berry-wayback/blob/e177f484aa3f8e5ad8fcb0619250edf0db242d47/indexer.js#L23-L39)

> Fetch 50 edits randomly from all the edits or edits by a user

```js
async function findEdits(accountId) {
    console.log('findEdits', accountId);

    return withPgClient(async client => {
        const { rows } = await client.query(`
            SELECT included_in_block_timestamp AS block_timestamp, included_in_block_hash AS block_hash
            FROM receipts ${accountId ? '' : `TABLESAMPLE SYSTEM(0.05)`}
            WHERE receiver_account_id = 'berryclub.ek.near'
                ${accountId ? `AND predecessor_account_id = $1` : ''}
            ORDER BY random()
            LIMIT 50
            `, accountId ? [accountId] : []);

        console.log(`Found ${rows.length} rows`);
        return rows;
    })
}
```

### Wallet Contract Helper

NEAR Wallet used indexer to query necessary information related to accounts, such as activities, FTs/NFTs owned by the user, etc.

- [indexer client source code](https://github.com/near/near-contract-helper/blob/master/middleware/indexer.js)

**findAccountActivity**: [source code](https://github.com/near/near-contract-helper/blob/7d6bd7efe4fe416747c8a23d40259286e636e49d/middleware/indexer.js#L39-L67)


> Show account activities in wallet

```js
const findAccountActivity = async (ctx) => {
    const { accountId } = ctx.params;
    let { offset, limit = 10 } = ctx.request.query;
    if (!offset) {
        offset = '9999999999999999999';
    }
    const { rows } = await pool.query(`
        select
            included_in_block_hash block_hash,
            included_in_block_timestamp block_timestamp,
            originated_from_transaction_hash hash,
            index_in_action_receipt action_index,
            predecessor_account_id signer_id,
            receiver_account_id receiver_id,
            action_kind,
            args
        from action_receipt_actions
        join receipts using(receipt_id)
        where
            receipt_predecessor_account_id != 'system' and
            (receipt_predecessor_account_id = $1 or receipt_receiver_account_id = $1) and
            $2 > receipt_included_in_block_timestamp
        order by receipt_included_in_block_timestamp desc
        limit $3
        ;
    `, [accountId, offset, limit]);

    ctx.body = rows;
};
```

**findAccountsByPublicKey**: [source code](https://github.com/near/near-contract-helper/blob/7d6bd7efe4fe416747c8a23d40259286e636e49d/middleware/indexer.js#L69-L80)

> Find accounts by public key, so when you input seed phrase, wallet is able to know which account is related to this seed phrase.

```js
const findAccountsByPublicKey = async (ctx) => {
    const { publicKey } = ctx.params;
    const { rows } = await pool.query(`
        SELECT DISTINCT account_id
        FROM access_keys
        JOIN accounts USING (account_id)
        WHERE public_key = $1
            AND accounts.deleted_by_receipt_id IS NULL
            AND access_keys.deleted_by_receipt_id IS NULL
    `, [publicKey]);
    ctx.body = rows.map(({ account_id }) => account_id);
};
```

**findLikelyTokens**: [source code](https://github.com/near/near-contract-helper/blob/7d6bd7efe4fe416747c8a23d40259286e636e49d/middleware/indexer.js#L94-L127)

> List the FT tokens related to the current user.

```js
const findLikelyTokens = async (ctx) => {
    const { accountId } = ctx.params;

    const received = `
        select distinct receipt_receiver_account_id as receiver_account_id
        from action_receipt_actions
        where args->'args_json'->>'receiver_id' = $1
            and action_kind = 'FUNCTION_CALL'
            and args->>'args_json' is not null
            and args->>'method_name' in ('ft_transfer', 'ft_transfer_call')
    `;

    const mintedWithBridge = `
        select distinct receipt_receiver_account_id as receiver_account_id from (
            select args->'args_json'->>'account_id' as account_id, receipt_receiver_account_id
            from action_receipt_actions
            where action_kind = 'FUNCTION_CALL' and
                receipt_predecessor_account_id = $2 and
                args->>'method_name' = 'mint'
        ) minted_with_bridge
        where account_id = $1
    `;

    const calledByUser = `
        select distinct receipt_receiver_account_id as receiver_account_id
        from action_receipt_actions
        where receipt_predecessor_account_id = $1
            and action_kind = 'FUNCTION_CALL'
            and (args->>'method_name' like 'ft_%' or args->>'method_name' = 'storage_deposit')
    `;

    const { rows } = await pool.query([received, mintedWithBridge, calledByUser].join(' union '), [accountId, BRIDGE_TOKEN_FACTORY_ACCOUNT_ID]);
    ctx.body = rows.map(({ receiver_account_id }) => receiver_account_id);
};
```

**findLikelyNFTs**: [source code](https://github.com/near/near-contract-helper/blob/7d6bd7efe4fe416747c8a23d40259286e636e49d/middleware/indexer.js#L130-L146)

> List the NFT tokens related to the current user.

```js
const findLikelyNFTs = async (ctx) => {
    const { accountId } = ctx.params;

    const received = `
        select distinct receipt_receiver_account_id as receiver_account_id
        from action_receipt_actions
        where args->'args_json'->>'receiver_id' = $1
            and action_kind = 'FUNCTION_CALL'
            and args->>'args_json' is not null
            and args->>'method_name' like 'nft_%'
    `;

    // TODO: How to query minted tokens?

    const { rows } = await pool.query([received].join(' union '), [accountId]);
    ctx.body = rows.map(({ receiver_account_id }) => receiver_account_id);
};
```
