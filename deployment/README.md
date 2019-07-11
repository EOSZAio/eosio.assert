# deployment of eosio.assert

## Build multisig transaction

### Transaction to create eosio.assert
```bash
cleos --url https://api.telos.africa:4443 system newaccount -sjd --stake-net "10.0000 TLOS" --stake-cpu "10.0000 TLOS" --buy-ram-kbytes 460 --transfer eosio eosio.assert eosio@active eosio@active -p eosio@active > deployment/account.trx.json
```

### Build eosio.assert contract

```bash
eosio-cpp -Oz -o eosio.assert.wasm -I ./eosio.assert/src -I ./eosio.assert/include eosio.assert/src/eosio.assert.cpp --abigen
```

### Transaction to deploy eosio.assert contract

```bash
cleos --url https://api.telos.africa:4443 set contract -sjd eosio.assert ./ -p eosio.assert@active  > deployment/assert.trx.json
```

### Merge account creation and contract deployment

The above actions resulted in the creation of the following files;
* deployment/account.trx.json : contains the transactions to create "eosio.assert" account
* deployment/assert.trx.json  : contains the transaction to deploy "eosio.assert" contract code to "eosio.assert"

Transactions from these files have been merged into a single file so that all transactions can be approved as a single multisig. Merge results are contained in;
* deployment/trx.json

## Propose transaction

### Stagenet
```bash
cleos --url http://stagenet.telosusa.io multisig propose_trx eosio.assert eosio@active ./deployment/trx.json rorymapatone -p rorymapstone@active
```

### Mainnet
```bash
cleos --url https://api.telos.africa:4443 multisig propose_trx eosio.assert eosio@active ./deployment/trx.json rorymapatone -p rorymapstone@active
```

### Review transaction

```bash
cleos --url https://<API point> multisig review rorymapstone eosio.assert
```

### Approve transaction

```bash
multisig approve rorymapstone eosio.assert '{"actor": "YOU", "permission": "active"}' -p YOU@active
```
