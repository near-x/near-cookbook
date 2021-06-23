# Play with NEAR CLI


### Create `xxx.near` or `xxx.testnet` Account

```bash
near call testnet create_account '{"new_account_id": "xxx.testnet", "new_public_key": "ed25519:DAh6wwBYbakodZaA3QSZvojBF7EceeUkZsdELTEBRoYt"}' --accountId bot.testnet --deposit 0.1
```

### Send NEP141 token to another account

#### TestNet

You need to run `storage_deposit` first to register an account

```bash
near call berryclub.testnet storage_deposit '{"account_id": "r1.testnet"}' --account-id bot.testnet --amount 0.025
```

Then you can send some tokens to the account

```bash
near call berryclub.testnet ft_transfer '{"receiver_id": "r1.testnet", "amount": "1"}' --account-id closure.testnet --amount 0.000000000000000000000001
```

#### MainNet

```bash
near call berryclub.ek.near storage_deposit '{"account_id": "r12.near"}' --account-id robertyan.near --amount 0.025
```

```bash
near call berryclub.ek.near ft_transfer '{"receiver_id": "r12.near", "amount": "1"}' --account-id robertyan.near --amount 0.000000000000000000000001
```
