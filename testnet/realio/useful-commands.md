***Information***

```bash
# Check the blocks
realio-networkd status 2>&1 | jq ."SyncInfo"."latest_block_height"
```
```bash
# Check logs
journalctl -u realio-networkd -f -o cat
```
```bash
# Check status
realio-networkd status 2>&1 | jq .SyncInfo
```
```bash
# Check balance
realio-networkd q bank balances $REALIO_WALLET_NAME
```
```bash
# Check pubkey of validator
realio-networkd tendermint show-validator
```
```bash
# Check validator
realio-networkd q staking validator $REALIO_VALOPER
realio-networkd q staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker="$REALIO_VALOPER")' | jq
```
```bash
# Check information of TX_HASH
realio-networkd q tx <TX_HASH>
```
```bash
# Check how many blocks were passed by the validator and from which block the asset
realio-networkd q slashing signing-info $(realio-networkd tendermint show-validator)
```

***Transactions***

```bash
# Collect rewards from all validators delegated to them (without commission)
realio-networkd tx distribution withdraw-all-rewards --from wallet --fees 5000000000000000ario --gas=300000 -y
```
```bash
# Collect rewards from a separate validator or rewards + commission from your own validator
realio-networkd tx distribution withdraw-rewards $REALIO_VALOPER --from wallet --fees 5000000000000000ario --gas=300000 --commission -y
```
```bash
# Delegate yourself (this is how 1 coin is sent)
realio-networkd tx staking delegate $REALIO_VALOPER 1000000000000000000ario --from wallet --fees 5000000000000000ario --gas=300000 -y
```
```bash
# Redelegate to other validator
realio-networkd tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000000000000000ario --from wallet --fees 5000000000000000ario --gas=300000 -y
```
```bash
# unbond 
realio-networkd tx staking unbond $REALIO_VALOPER 1000000000000000000ario --from wallet --fees 5000000000000000ario --gas=300000 -y
```
```bash
# Send tokens to other adress
realio-networkd tx bank send wallet <address> 1000000000000000000ario --fees 5000000000000000ario --gas=300000 -y
```
```bash
# Escape from jail
realio-networkd tx slashing unjail --from wallet --fees 5000000000000000ario --gas=300000 -y
```

**! If the transactions are not sent with the error account sequence mismatch, expected 18, got 17: incorrect account sequence, then add the flag -s 18 to the command (replace the number with the one that is waiting for the sequence)**

***Work with wallets***

```bash
# Chech the wallets list
realio-networkd keys list
```
```bash
# Delete wallet
realio-networkd keys delete <name_wallet>
```

***Delete Node***

```bash
sudo systemctl stop realio-networkd && \
sudo systemctl disable realio-networkd && \
rm /etc/systemd/system/realio-networkd.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf realio-networkd && \
rm -rf .realio-networkd && \
rm -rf $(which realio-networkd)
```

***Governance***
```bash
# List proposals
realio-networkd q gov proposals
```
```bash
# Check voting result
realio-networkd q gov proposals --voter $REALIO_VALOPER
```
```bash
# Vote 
realio-networkd tx gov vote 1 yes --from $REALIO_WALLET_NAME
Peers and RPC
FOLDER=.realio-networkd
```
```bash
# Check your peer
PORTR=$(grep -A 3 "\[p2p\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+") && \
echo $(realio-networkd tendermint show-node-id)@$(curl ifconfig.me)$PORTR
```
```bash
# Check port of RPC
echo -e "\033[0;32m$(grep -A 3 "\[rpc\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+")\033[0m"
```
```bash
# Check peers quantity
PORT=
curl -s http://localhost:$REALIO_PORT/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr | split(":")[2])"' | wc -l
```
