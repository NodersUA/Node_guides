***Information***
```bash
# Check the blocks
nolusd status 2>&1 | jq ."SyncInfo"."latest_block_height"
```
```bash
# Check logs
journalctl -u nolusd -f -o cat
```
```bash
# Check status
nolusd status 2>&1 | jq .SyncInfo
```
```bash
# Check balance
nolusd q bank balances $WALLET_NOLUS
```
```bash
# Check pubkey of validator
nolusd tendermint show-validator
```
```bash
# Check validator
nolusd q staking validator $VALOPER_NOLUS
nolusd q staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker="$MONIKER_NOLUS")' | jq
```
```bash
# Check information of TX_HASH
nolusd q tx <TX_HASH>
```
```bash
# Check how many blocks were passed by the validator and from which block the asset
nolusd q slashing signing-info $(nolusd tendermint show-validator)
```

***Transactions***
```bash
# Collect rewards from all validators delegated to them (without commission)
nolusd tx distribution withdraw-all-rewards --from <name_wallet> --fees 5000unls -y
```
```bash
# Collect rewards from a separate validator or rewards + commission from your own validator
nolusd tx distribution withdraw-rewards ${VALOPER_NOLUS} --from <name_wallet> --fees 5000unls --commission -y
```
```bash
# Delegate yourself (this is how 1 coin is sent)
nolusd tx staking delegate ${VALOPER_NOLUS} 1000000unls --from <name_wallet> --fees 5000unls -y
```
```bash
# Redelegate to other validator
nolusd tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000unls --from <name_wallet> --fees 5000unls -y
```
```bash
# Unbond 
nolusd tx staking unbond ${VALOPER_NOLUS} 1000000unls --from <name_wallet> --fees 5000unls -y
```
```bash
# Send tokens to other adress
nolusd tx bank send <name_wallet> <address> 1000000unls --fees 5000unls -y
```
```bash
# Unjail
nolusd tx slashing unjail --from <name_wallet> --fees 5000unls -y
```

! If the transactions are not sent with the error account sequence mismatch, expected 18, got 17: incorrect account sequence, then add the flag -s 18 to the command (replace the number with the one that is waiting for the sequence)

***Work with wallets***
```bash
# Chech the wallets list
nolusd keys list
```
```bash
# Delete wallet
nolusd keys delete <name_wallet>
```

***Delete Node***
```bash
sudo systemctl stop nolusd && \
sudo systemctl disable nolusd && \
rm /etc/systemd/system/nolusd.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf nolus && \
rm -rf .nolus && \
rm -rf $(which nolusd)
```

***Governance***
```bash
# List proposals
nolusd q gov proposals
```
```bash
# Check voting result
nolusd q gov proposals --voter $VALOPER_NOLUS
```
```bash
# Vote
nolusd tx gov vote 1 yes --from $WALLET_NOLUS
```
```bash
# Make Deposit for proposal
nolusd tx gov deposit 1 1000000unls --from $WALLET_NOLUS
```

***Peers and RPC***
```bash
FOLDER=.nolus

# Check your peer
PORTR=$(grep -A 3 "\[p2p\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+") && \
echo $(nolusd tendermint show-node-id)@$(curl ifconfig.me)$PORTR

# Check port of RPC
echo -e "\033[0;32m$(grep -A 3 "\[rpc\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+")\033[0m"

# Check peers quantity
PORT=
curl -s http://localhost:$PORT_NOLUS/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr | split(":")[2])"' | wc -l
```

