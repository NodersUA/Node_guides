***Information***
```bash
# Check the blocks
saod status 2>&1 | jq ."SyncInfo"."latest_block_height"
```
```bash
# Check logs
journalctl -u saod -f -o cat
```
```bash
# Check status
saod status 2>&1 | jq .SyncInfo
```
```bash
# Check balance
saod q bank balances $WALLET_SAO
```
```bash
# Check pubkey of validator
saod tendermint show-validator
```
```bash
# Check validator
saod q staking validator $VALOPER_SAO
saod q staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker="$MONIKER_SAO")' | jq
```
```bash
# Check information of TX_HASH
saod q tx <TX_HASH>
```
```bash
# Check how many blocks were passed by the validator and from which block the asset
saod q slashing signing-info $(saod tendermint show-validator)
```

***Transactions***
```bash
# Collect rewards from all validators delegated to them (without commission)
saod tx distribution withdraw-all-rewards --from <name_wallet> --fees 5000sao -y
```
```bash
# Collect rewards from a separate validator or rewards + commission from your own validator
saod tx distribution withdraw-rewards ${VALOPER_SAO} --from <name_wallet> --fees 5000sao --commission -y
```
```bash
# Delegate yourself (this is how 1 coin is sent)
saod tx staking delegate ${VALOPER_SAO} 1000000sao --from <name_wallet> --fees 5000sao -y
```
```bash
# Redelegate to other validator
saod tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000sao --from <name_wallet> --fees 5000sao -y
```
```bash
# Unbond 
saod tx staking unbond ${VALOPER_SAO} 1000000sao --from <name_wallet> --fees 5000sao -y
```
```bash
# Send tokens to other adress
saod tx bank send <name_wallet> <address> 1000000sao --fees 5000sao -y
```
```bash
# Unjail
saod tx slashing unjail --from <name_wallet> --fees 5000sao -y
```

! If the transactions are not sent with the error account sequence mismatch, expected 18, got 17: incorrect account sequence, then add the flag -s 18 to the command (replace the number with the one that is waiting for the sequence)

***Work with wallets***
```bash
# Chech the wallets list
saod keys list
```
```bash
# Delete wallet
saod keys delete <name_wallet>
```

***Delete Node***
```bash
sudo systemctl stop saod && \
sudo systemctl disable saod && \
rm /etc/systemd/system/saod.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf sao && \
rm -rf .sao && \
rm -rf $(which saod)
```

***Governance***
```bash
# List proposals
saod q gov proposals
```
```bash
# Check voting result
saod q gov proposals --voter $VALOPER_SAO
```
```bash
# Vote
saod tx gov vote 1 yes --from $WALLET_SAO
```
```bash
# Make Deposit for proposal
saod tx gov deposit 1 1000000sao --from $WALLET_SAO
```

***Peers and RPC***
```bash
FOLDER=.sao

# Check your peer
PORTR=$(grep -A 3 "\[p2p\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+") && \
echo $(saod tendermint show-node-id)@$(curl ifconfig.me)$PORTR

# Check port of RPC
echo -e "\033[0;32m$(grep -A 3 "\[rpc\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+")\033[0m"

# Check peers quantity
PORT=
curl -s http://localhost:$PORT_SAO/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr | split(":")[2])"' | wc -l
```
