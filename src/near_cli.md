# Play with NEAR CLI


### Create `xxx.near` or `xxx.testnet` Account

```bash
near call testnet create_account \
'{
    "new_account_id": "xxxxxxx.testnet", 
    "new_public_key": "ed25519:DAh6wwBYbakodZaA3QSZvojBF7EceeUkZsdELTEBRoYt"
}' \
--accountId bot.testnet --deposit 0.1
```

### Send NEP141 token to another account

Here we take `Banana` from BerryClub for example. 

Suppose we want to transfer 1 Banana from the user `robertyan.testnet` to `linus.testnet`. 
1. If the receiver hasn't regisered on NEP141 before, we first need to deposit storage cost for the receiver `linus.testnet` (i.e. register the account on the NEP141 contract) with a small amount of NEAR. 
2. Then we'll be able to call `ft_transfer` to transfer Banana to the receiver. Since the precision of Banana is 18 digits, the amount is `1000000000000000000` for sending 1 Banana

#### TestNet

You need to run `storage_deposit` first to register the receiver account on Banana contract `berryclub.testnet`

```bash
near call berryclub.testnet \
storage_deposit '{"account_id": "linus.testnet"}' \
--account-id robertyan.testnet --amount 0.025
```

Then you can send some tokens to the receiver

```bash
near call berryclub.testnet ft_transfer \
'{"receiver_id": "linus.testnet", "amount": "1000000000000000000"}' \
--account-id robertyan.testnet --amount 0.000000000000000000000001
```

#### MainNet

You need to run `storage_deposit` first to register the receiver account on Banana contract `berryclub.ek.near`


```bash
near call berryclub.ek.near storage_deposit '{"account_id": "linus.near"}' \
--account-id robertyan.near --amount 0.025
```

Then you can send some tokens to the receiver

```bash
near call berryclub.ek.near ft_transfer \
'{"receiver_id": "linus.near", "amount": "1000000000000000000"}' \
--account-id robertyan.near --amount 0.000000000000000000000001
```
