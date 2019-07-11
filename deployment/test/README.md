# Simulation

```bash
echo curl -X POST http://testnet.telos.africa:8887/v1/chain/get_account -H \'Content-Type: application/json\' -d \'{"\""account_name"\"":"\""eosiotestacc"\""}\' \| jq [\'.permissions[0].required_auth.accounts[].permission]\' | bash - > signatories.json
```

## Test accounts

* eosiotestacc
* msigtester11
* msigtester22
* msigtester33

```bash
cleos --url http://testnet.telos.africa:8887 system newaccount --stake-net "0.1000 TLOS" --stake-cpu "0.9000 TLOS" --buy-ram-kbytes 5 --transfer southafrica1 eosiotestacc EOS5mHoFGLzdbcsng6XY7ZMbKCCQ4EQ7egkwQ7sSiWQNJEpgV8oEn EOS5mHoFGLzdbcsng6XY7ZMbKCCQ4EQ7egkwQ7sSiWQNJEpgV8oEn
cleos --url http://testnet.telos.africa:8887 system newaccount --stake-net "0.1000 TLOS" --stake-cpu "0.9000 TLOS" --buy-ram-kbytes 5 --transfer southafrica1 msigtester11 EOS5mHoFGLzdbcsng6XY7ZMbKCCQ4EQ7egkwQ7sSiWQNJEpgV8oEn EOS5mHoFGLzdbcsng6XY7ZMbKCCQ4EQ7egkwQ7sSiWQNJEpgV8oEn
cleos --url http://testnet.telos.africa:8887 system newaccount --stake-net "0.1000 TLOS" --stake-cpu "0.9000 TLOS" --buy-ram-kbytes 5 --transfer southafrica1 msigtester22 EOS5mHoFGLzdbcsng6XY7ZMbKCCQ4EQ7egkwQ7sSiWQNJEpgV8oEn EOS5mHoFGLzdbcsng6XY7ZMbKCCQ4EQ7egkwQ7sSiWQNJEpgV8oEn
cleos --url http://testnet.telos.africa:8887 system newaccount --stake-net "0.1000 TLOS" --stake-cpu "0.9000 TLOS" --buy-ram-kbytes 5 --transfer southafrica1 msigtester33 EOS5mHoFGLzdbcsng6XY7ZMbKCCQ4EQ7egkwQ7sSiWQNJEpgV8oEn EOS5mHoFGLzdbcsng6XY7ZMbKCCQ4EQ7egkwQ7sSiWQNJEpgV8oEn
```

###Create multisig permission

```bash
cleos --url http://testnet.telos.africa:8887 set account permission eosiotestacc active '{"threshold":2,"keys":[],"accounts":[{"permission":{"actor":"msigtester11","permission":"active"},"weight":1},{"permission":{"actor":"msigtester22","permission":"active"},"weight":1},{"permission":{"actor":"msigtester33","permission":"active"},"weight":1}],"waits":[]}' owner -p eosiotestacc@owner
```

### Fund eosiotestacc

```bash
cleos --url http://testnet.telos.africa:8887 push action eosio.token transfer '[ "southafrica1", "eosiotestacc", "100.0000 TLOS", "Testing eosio.assert deployment" ]' -p southafrica1@active
```

### Transaction to create eosio.assert

* eosioassert1

```bash
cleos --url http://testnet.telos.africa:8887 system newaccount -sjd --stake-net "10.0000 TLOS" --stake-cpu "10.0000 TLOS" --buy-ram-kbytes 460 --transfer eosiotestacc eosioassert1 eosiotestacc@active eosiotestacc@active -p eosiotestacc@active > deployment/test/account.trx.json
```

### Transaction to deploy eosio.assert contract

```bash
cleos --url http://testnet.telos.africa:8887 set contract -sjd eosioassert1 ./ -p eosioassert1@active  > deployment/test/assert.trx.json
```

### Merge transactions


### Propose multisig

account southafrica1 has insufficient ram; needs 37025 bytes has 15659 bytes

cleos --url http://testnet.telos.africa:8887 system buyram southafrica1 southafrica1 --kbytes 24

```bash
cleos --url http://testnet.telos.africa:8887 multisig propose_trx eosio.assert ./deployment/test/signatories.json ./deployment/test/trx.json southafrica1 -p southafrica1@active
```

### Review transaction

```bash
cleos --url http://testnet.telos.africa:8887 multisig review southafrica1 eosio.assert
```

### Approve transacton

```bash
cleos  --url http://testnet.telos.africa:8887 multisig approve southafrica1 eosio.assert '{"actor": "msigtester11", "permission": "active"}' -p msigtester11@active
cleos  --url http://testnet.telos.africa:8887 multisig approve southafrica1 eosio.assert '{"actor": "msigtester22", "permission": "active"}' -p msigtester22@active
cleos  --url http://testnet.telos.africa:8887 multisig approve southafrica1 eosio.assert '{"actor": "msigtester33", "permission": "active"}' -p msigtester33@active
```

```bash
cleos  --url http://testnet.telos.africa:8887 multisig exec southafrica1 eosio.assert southafrica1 -p southafrica1
```
