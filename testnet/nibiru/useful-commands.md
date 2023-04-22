***Information***
```bash
# Check the blocks
nibid status 2>&1 | jq ."SyncInfo"."latest_block_height"
```
```bash
# Restart
systemctl restart nibid && journalctl -u nibid -f -o cat
```
```bash
# Check logs
journalctl -u nibid -f -o cat
```
```bash
# Check status
nibid status 2>&1 | jq .SyncInfo
```
```bash
# Check balance
nibid q bank balances $NIBIRU_ADDRESS
```
```bash
# Check pubkey of validator
nibid tendermint show-validator
```
```bash
# Check validator
nibid q staking validator $NIBIRU_VALOPER
nibid q staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker="$NIBIRU_VALOPER")' | jq
```
```bash
# Check information of TX_HASH
nibidd q tx <TX_HASH>
```
```bash
# Check how many blocks were passed by the validator and from which block the asset
nibidd q slashing signing-info $(nibid tendermint show-validator)
```

***Transactions***
```bash
# Collect rewards from all validators delegated to them (without commission)
nibid tx distribution withdraw-all-rewards --from wallet --fees 7500unibi --gas=300000 -y
```
```bash
# Collect rewards from a separate validator or rewards + commission from your own validator
nibid tx distribution withdraw-rewards $NIBIRU_VALOPER --from wallet --fees 12500unibi --gas=500000 --commission -y
```
```bash
# Delegate yourself (this is how 1 coin is sent)
nibid tx staking delegate $NIBIRU_VALOPER 1000000unibi --from wallet --fees 12500unibi --gas=500000 -y
```
```bash
# Redelegate to other validator
nibid tx staking redelegate $NIBIRU_VALOPER <dst-validator-addr> 1000000unibi --from wallet --fees 7500unibi --gas=300000 -y
```
```bash
# unbond 
nibid tx staking unbond $NIBIRU_VALOPER 1000000unibi --from wallet --fees 7500unibi --gas=300000 -y
```
```bash
# Send tokens to other adress
nibid tx bank send wallet <address> 1000000unibi --fees 7500unibi --gas=300000 -y
```
```bash
# Escape from jail
nibid tx slashing unjail --from wallet --fees 7500unibi --gas=300000
```

! If the transactions are not sent with the error account sequence mismatch, expected 18, got 17: incorrect account sequence, then add the flag -s 18 to the command (replace the number with the one that is waiting for the sequence)

***Work with wallets***
```bash
# Chech the wallets list
nibid keys list
```
```bash
# Delete wallet
nibid keys delete <name_wallet>
```

***Delete Node***
```bash
sudo systemctl stop nibid && \
sudo systemctl disable nibid && \
rm /etc/systemd/system/nibid.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf nibid && \
rm -rf .nibid && \
rm -rf $(which nibid)
```

***Governance***
```bash
# List proposals
nibid q gov proposals
```
```bash
# Check voting result
nibid q gov proposals --voter $NIBIRU_VALOPER
```
```bash
# Vote 
nibid tx gov vote 1 yes --from $NIBIRU_ADRESS
```

***Peers and RPC***
```bash
FOLDER=.nibid

# Check your peer
PORTR=$(grep -A 3 "\[p2p\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+") && \
echo $(nibid tendermint show-node-id)@$(curl ifconfig.me)$PORTR

# Check port of RPC
echo -e "\033[0;32m$(grep -A 3 "\[rpc\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+")\033[0m"

# Check peers quantity
PORT=
curl -s http://localhost:$NIBIRU_PORT/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr | split(":")[2])"' | wc -l
```

