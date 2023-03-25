***Information***
```bash
# Check the blocks
ojod status 2>&1 | jq ."SyncInfo"."latest_block_height"
```
```bash
# Check logs
journalctl -u ojod -f -o cat
```
```bash
# Check status
ojod status 2>&1 | jq .SyncInfo
```
```bash
# Check balance
ojod q bank balances $WALLET_OJO
```
```bash
# Check pubkey of validator
ojod tendermint show-validator
```
```bash
# Check validator
ojod q staking validator $VALOPER_OJO
dymd q staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker="$MONIKER_OJO")' | jq
```
```bash
# Check information of TX_HASH
ojod q tx <TX_HASH>
```
```bash
# Check how many blocks were passed by the validator and from which block the asset
ojod q slashing signing-info $(ojod tendermint show-validator)
```

***Transactions***
```bash
# Collect rewards from all validators delegated to them (without commission)
ojod tx distribution withdraw-all-rewards --from <name_wallet> --fees 5000uojo -y
```
```bash
# Collect rewards from a separate validator or rewards + commission from your own validator
ojod tx distribution withdraw-rewards ${VALOPER_OJO} --from <name_wallet> --fees 5000uojo --commission -y
```
```bash
# Delegate yourself (this is how 1 coin is sent)
ojod tx staking delegate ${VALOPER_OJO} 1000000uojo --from <name_wallet> --fees 5000uojo -y
```
```bash
# Redelegate to other validator
ojod tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000uojo --from <name_wallet> --fees 5000uojo -y
```
```bash
# Unbond 
ojod tx staking unbond ${VALOPER_OJO} 1000000uojo --from <name_wallet> --fees 5000uojo -y
```
```bash
# Send tokens to other adress
ojod tx bank send <name_wallet> <address> 1000000uojo --fees 5000uojo -y
```
```bash
# Unjail
ojod tx slashing unjail --from <name_wallet> --fees 5000uojo -y
```

! If the transactions are not sent with the error account sequence mismatch, expected 18, got 17: incorrect account sequence, then add the flag -s 18 to the command (replace the number with the one that is waiting for the sequence)

***Work with wallets***
```bash
# Chech the wallets list
ojod keys list
```
```bash
# Delete wallet
ojod keys delete <name_wallet>
```

***Delete Node***
```bash
sudo systemctl stop ojod && \
sudo systemctl disable ojod && \
rm /etc/systemd/system/ojod.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf ojo && \
rm -rf .ojo && \
rm -rf $(which ojod)
```

***Governance***
```bash
# List proposals
ojod q gov proposals
```
```bash
# Check voting result
ojod q gov proposals --voter $VALOPER_OJO
```
```bash
# Vote
ojod tx gov vote 1 yes --from $WALLET_OJO
```
```bash
# Make Deposit for proposal
ojod tx gov deposit 1 1000000uojo --from $WALLET_OJO
```

***Peers and RPC***
```bash
FOLDER=.ojo

# Check your peer
PORTR=$(grep -A 3 "\[p2p\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+") && \
echo $(ojod tendermint show-node-id)@$(curl ifconfig.me)$PORTR

# Check port of RPC
echo -e "\033[0;32m$(grep -A 3 "\[rpc\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+")\033[0m"

# Check peers quantity
PORT=
curl -s http://localhost:$PORT_OJO/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr | split(":")[2])"' | wc -l
```

