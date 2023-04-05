***Information***
```bash
# Check the balance
gitopiad status 2>&1 | jq ."SyncInfo"."latest_block_height"
```
```bash
# Check logs
journalctl -u gitopiad -f -o cat
```
```bash
# Check status
gitopiad status 2>&1 | jq .SyncInfo
```
```bash
# Check balance
gitopiad q bank balances $GITOPIA_ADDRESS
```
```bash
# Check pubkey of validator
gitopiad tendermint show-validator
```
```bash
# Check validator
gitopiad q staking validator $GITOPIA_VALOPER
gitopiad q staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker="$GITOPIA_VALOPER")' | jq
```
```bash
# Check information of TX_HASH
gitopiad q tx <TX_HASH>
```
```bash
# Check how many blocks were passed by the validator and from which block the asset
gitopiad q slashing signing-info $(gitopiad tendermint show-validator)
```

***Transactions***

```bash
# Collect rewards from all validators delegated to them (without commission)
gitopiad tx distribution withdraw-all-rewards --from wallet --fees 5000utlore -y
```
```bash
# Collect rewards from a separate validator or rewards + commission from your own validator
gitopiad tx distribution withdraw-rewards $GITOPIA_VALOPER --from wallet --fees 5000utlore --commission -y
```
```bash
# Delegate yourself (this is how 1 coin is sent)
gitopiad tx staking delegate $GITOPIA_VALOPER 1000000utlore --from wallet --fees 5000utlore -y
```
```bash
# Redelegate to other validator
gitopiad tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000utlore --from wallet --fees 5000utlore --gas=300000 -y
```
```bash
# unbond 
gitopiad tx staking unbond $GITOPIA_VALOPER 1000000utlore --from wallet --fees 5000utlore -y
```
```bash
# Send tokens to other adress
gitopiad tx bank send wallet <address> 1000000utlore --fees 5000utlore -y
```
```bash
# Escape from jail
gitopiad tx slashing unjail --from wallet --fees 5000utlore -y
```

! If the transactions are not sent with the error account sequence mismatch, expected 18, got 17: incorrect account sequence, then add the flag -s 18 to the command (replace the number with the one that is waiting for the sequence)

**Work with wallets**

```bash
# Chech the wallets list
gitopiad keys list
```
```bash
# Delete wallet
gitopiad keys delete <name_wallet>
```

**Delete Node**

```bash
sudo systemctl stop gitopiad && \
sudo systemctl disable gitopiad && \
rm /etc/systemd/system/gitopiad.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf gitopia && \
rm -rf .gitopia && \
rm -rf $(which gitopiad)
```

**Governance**

```bash
# List proposals
gitopiad q gov proposals
```
```bash
# Check voting result
gitopiad q gov proposals --voter $GITOPIA_VALOPER
```
```bash
# Vote 
gitopiad tx gov vote 1 yes --from $GITOPIA_WALLET_NAME
```
```bash
# Make a deposit in the proposal
gitopiad tx gov deposit 1 1000000utlore --from $GITOPIA_WALLET_NAME
```

**Peers and RPC**

```bash
FOLDER=.gitopiad
```
```bash
# Check your peer
PORTR=$(grep -A 3 "\[p2p\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+") && \
echo $(gitopiad tendermint show-node-id)@$(curl ifconfig.me)$PORTR
```
```bash
# Check port of RPC
echo -e "\033[0;32m$(grep -A 3 "\[rpc\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+")\033[0m"
```
```bash
# Check peers quantity
PORT=
curl -s http://localhost:$GITOPIA_PORT/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr | split(":")[2])"' | wc -l
```
