# deployment of eosio.assert

### Stagenet 

* Stagenet : http://stagenet.telosusa.io
* Testnet  : http://testnet.telos.africa:8887
* Mainnet  : API="--url https://api.telos.africa:4443"

#Step 0

## Funding for account creation

```bash
cleos $API push action eosio.token transfer '[ "southafrica1", "eosio", "100.0000 TLOS", "Funding for resources required to create eosio.assert account" ]' -p southafrica1@active
```

#Step 1

## Build multisig transaction to create eosio.assert

```bash
cleos $API system newaccount -sjd --stake-net "10.0000 TLOS" --stake-cpu "10.0000 TLOS" --buy-ram-kbytes 300 --transfer eosio eosio.assert eosio@active eosio@active -p eosio@active > deployment/account.trx.json
```

## Propose creation of eosio.assert account

```bash
cleos $API multisig propose_trx assertcreate ./deployment/signatories.json ./deployment/account.trx.json southafrica1 -p southafrica1@active
```

## Review transaction

```bash
cleos $API multisig review southafrica1 assertcreate
```

## Approve transaction

```bash
cleos --url <API> multisig approve southafrica1 assertcreate '{"actor": "YOU", "permission": "active"}' -p YOU@active
```

```bash
cleos $API multisig approve southafrica1 assertcreate '{"actor": "southafrica1", "permission": "active"}' -p southafrica1@active
```

## Execute transaction

```bash
cleos  --url <API> multisig exec southafrica1 assertcreate YOU -p YOU

cleos  $API multisig exec southafrica1 assertcreate southafrica1 -p southafrica1
```

---
#Step 2

## Build multisig transaction to deploy eosio.assert

### Build eosio.assert contract

```bash
eosio-cpp -Oz -o eosio.assert.wasm -I ./eosio.assert/src -I ./eosio.assert/include eosio.assert/src/eosio.assert.cpp --abigen
```

## Transaction to deploy eosio.assert contract

```bash
cleos $API set contract -sjd eosio.assert ./ -p eosio.assert@active  > deployment/assert.trx.json
```

## Propose deployment of eosio.assert contract code

```bash
cleos $API multisig propose_trx assertdeploy ./deployment/signatories.json ./deployment/assert.trx.json southafrica1 -p southafrica1@active
```

## Review transaction

```bash
cleos $API multisig review southafrica1 assertdeploy
```

## Approve transaction

```bash
cleos --url <API> multisig approve southafrica1 assertcreate '{"actor": "YOU", "permission": "active"}' -p YOU@active
```

```bash
cleos $API multisig approve southafrica1 assertdeploy '{"actor": "southafrica1", "permission": "active"}' -p southafrica1@active
```
