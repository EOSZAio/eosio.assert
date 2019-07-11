# deployment of eosio.assert

### Stagenet 

* Stagenet : http://stagenet.telosusa.io
* Testnet  : http://testnet.telos.africa:8887
* Mainnet  : https://api.telos.africa:4443

```bash
echo curl -X POST http://testnet.telos.africa:8887/v1/chain/get_account -H \'Content-Type: application/json\' -d \'{"\""account_name"\"":"\""eosio.prods"\""}\' \| jq [\'.permissions[0].required_auth.accounts[].permission]\' | bash - > deployment/signatories.json
```

NB need all active producers because of rotation schedule on testnet

## Build multisig transaction to create eosio.assert

```bash
cleos --url http://testnet.telos.africa:8887 system newaccount -sjd --stake-net "10.0000 TLOS" --stake-cpu "10.0000 TLOS" --buy-ram-kbytes 300 --transfer eosio eosio.assert eosio@active eosio@active -p eosio@active > deployment/account.trx.json
```

## Propose creation of eosio.assert account

```bash
cleos --url http://testnet.telos.africa:8887 multisig propose_trx assertcreate ./deployment/signatories.json ./deployment/account.trx.json southafrica1 -p southafrica1@active
```

## Review transaction

```bash
cleos --url http://testnet.telos.africa:8887 multisig review southafrica1 assertcreate
```

## Approve transaction

```bash
cleos --url <API> multisig approve southafrica1 assertcreate '{"actor": "YOU", "permission": "active"}' -p YOU@active
```

```bash
cleos --url http://testnet.telos.africa:8887 multisig approve southafrica1 assertcreate '{"actor": "southafrica1", "permission": "active"}' -p southafrica1@active
```

---
## Build multisig transaction to deploy eosio.assert

### Build eosio.assert contract

```bash
eosio-cpp -Oz -o eosio.assert.wasm -I ./eosio.assert/src -I ./eosio.assert/include eosio.assert/src/eosio.assert.cpp --abigen
```

## Transaction to deploy eosio.assert contract

```bash
cleos --url http://testnet.telos.africa:8887 set contract -sjd eosio.assert ./ -p eosio.assert@active  > deployment/assert.trx.json
```

## Propose deployment of eosio.assert contract code

```bash
cleos --url http://testnet.telos.africa:8887 multisig propose_trx assertdeploy ./deployment/signatories.json ./deployment/assert.trx.json southafrica1 -p southafrica1@active
```

## Review transaction

```bash
cleos --url http://testnet.telos.africa:8887 multisig review southafrica1 assertdeploy
```

## Approve transaction

```bash
cleos --url <API> multisig approve southafrica1 assertcreate '{"actor": "YOU", "permission": "active"}' -p YOU@active
```

```bash
cleos --url http://testnet.telos.africa:8887 multisig approve southafrica1 assertdeploy '{"actor": "southafrica1", "permission": "active"}' -p southafrica1@active
```
