# Useful Commands

### Information

```bash
# Check the blocks
cascadiad status 2>&1 | jq ."SyncInfo"."latest_block_height"

# Check logs
journalctl -u cascadiad -f -o cat

# Restart
systemctl restart cascadiad && journalctl -u cascadiad -f -o cat

# Check status
cascadiad status 2>&1 | jq .SyncInfo

# Check balance
cascadiad q bank balances $CASCADIA_ADDRESS

# Check EVM address
cascadiad address-converter $(cascadiad keys show wallet -a)

# Check pubkey of validator
cascadiad tendermint show-validator

# Check validator
cascadiad q staking validator $CASCADIA_VALOPER
cascadiad q staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker="$CASCADIA_VALOPER")' | jq

# Check information of TX_HASH
cascadiad q tx <TX_HASH>

# Check how many blocks were passed by the validator and from which block the asset
cascadiad q slashing signing-info $(cascadiad tendermint show-validator)
```

```bash
Transactions

# Collect rewards from all validators delegated to them (without commission)
cascadiad tx distribution withdraw-all-rewards --from wallet --gas 300000 --gas-adjustment=1.4 --gas-prices=7aCC -y

# Collect rewards from a separate validator or rewards + commission from your own validator
cascadiad tx distribution withdraw-rewards $CASCADIA_VALOPER --from wallet --gas 300000 --gas-adjustment=1.4 --gas-prices=7aCC --commission -y

# Delegate yourself (this is how 1 coin is sent)
cascadiad tx staking delegate $CASCADIA_VALOPER 1000000000000000000aCC --from wallet --gas 300000 --gas-adjustment=1.4 --gas-prices=7aCC -y

# Redelegate to other validator
cascadiad tx staking redelegate $CASCADIA_VALOPER <dst-validator-addr> 1000000000000000000aCC --from wallet --gas 300000 --gas-adjustment=1.4 --gas-prices=7aCC -y

# unbond 
cascadiad tx staking unbond $CASCADIA_VALOPER 1000000000000000000aCC --from wallet --gas 300000 --gas-adjustment=1.4 --gas-prices=7aCC -y

# Send tokens to other adress
cascadiad tx bank send wallet <address> 1000000000000000000aCC --gas 300000 --gas-adjustment=1.4 --gas-prices=7aCC -y

# Escape from jail
cascadiad tx slashing unjail --from wallet --gas 300000 --gas-adjustment=1.4 --gas-prices=7aCC
```

! If the transactions are not sent with the error account sequence mismatch, expected 18, got 17: incorrect account sequence, then add the flag -s 18 to the command (replace the number with the one that is waiting for the sequence)

**Work with wallets**

```bash
# Chech the wallets list
cascadiad keys list

# Delete wallet
cascadiad keys delete <name_wallet>
```

**Delete Node**

```bash
sudo systemctl stop cascadiad && \
sudo systemctl disable cascadiad && \
rm /etc/systemd/system/cascadiad.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf cascadia && \
rm -rf .cascadiad && \
rm -rf $(which cascadiad)
```

Governance

```bash
# List proposals
cascadiad q gov proposals

# Check voting result
cascadiad q gov proposals --voter $CASCADIA_ADDRESS

# Vote 
cascadiad tx gov vote 20 yes --from wallet --gas 300000 --gas-adjustment=1.4 --gas-prices=7aCC -y
```

**Peers and RPC**

```bash
FOLDER=.cascadiad

# Check your peer
PORTR=$(grep -A 3 "\[p2p\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+") && \
echo $(cascadiad tendermint show-node-id)@$(curl ifconfig.me)$PORTR

# Check port of RPC
echo -e "\033[0;32m$(grep -A 3 "\[rpc\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+")\033[0m"

# Check peers quantity
PORT=
curl -s http://localhost:CASCADIA_PORT/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr | split(":")[2])"' | wc -l
```
