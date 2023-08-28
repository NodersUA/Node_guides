# Smart Contract Tasks

```bash
cd && git clone https://github.com/NibiruChain/cw-nibiru
```

```bash
nibid tx wasm store $HOME/cw-nibiru/artifacts-cw-plus/cw20_base.wasm --from wallet \
--gas-adjustment 1.2 --gas 8000000 --fees 200000unibi -y
```

```bash
txhash=<your_thash>
```

```bash
code_id=$(nibid q tx $txhash -o json | jq -r '.logs[].events[].attributes[] | select(.key=="code_id").value')
INIT="{\"name\":\"$NIBIRU_MONIKER\",\"symbol\":\"$(echo $(echo "$NIBIRU_MONIKER" | tr -d '[:punct:]') | cut -c1-4)\",\"decimals\":6,\"initial_balances\":[{\"address\":\"$NIBIRU_ADDRESS\",\"amount\":\"2000000\"}],\"mint\":{\"minter\":\"$NIBIRU_ADDRESS\"},\"marketing\":{}}"
nibid tx wasm instantiate $code_id "$INIT" --from wallet --label "$NIBIRU_MONIKER cw20_base" --gas-adjustment 1.2 --gas 8000000 --fees 200000unibi --no-admin -y
```

```bash
nibid keys add transfer_wallet
transfer_wallet=$(nibid keys show transfer_wallet -a)
BALANCE_QUERY="{\"balance\": {\"address\": \"$NIBIRU_ADDRESS\"}}"
TRANSFER="{\"transfer\":{\"recipient\":\"$transfer_wallet\",\"amount\":\"50\"}}"
CONTRACT=$(nibid query wasm list-contract-by-code $code_id --output json | jq -r '.contracts[-1]')
nibid tx wasm execute $CONTRACT $TRANSFER --gas-adjustment 1.2 --gas 8000000 --fees 200000unibi --from wallet --chain-id $NIBIRU_CHAIN_ID -y
nibid query wasm contract-state smart $CONTRACT "$BALANCE_QUERY" --output json
```

```
```
