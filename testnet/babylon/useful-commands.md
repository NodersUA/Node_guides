# Useful Commands

## Information

```bash
# Check the blocks
babylond status 2>&1 | jq ."SyncInfo"."latest_block_height"

# Check logs
journalctl -u babylond -f -o cat

# Restart
systemctl restart babylond && journalctl -u babylond -f -o cat

# Check status
babylond status 2>&1 | jq .SyncInfo

# Check balance
babylond q bank balances $BABYLON_ADDRESS

# Check EVM address
babylond address-converter $(babylond keys show wallet -a)

# Check pubkey of validator
babylond tendermint show-validator

# Check validator
babylond q staking validator $BABYLON_VALOPER
babylond q staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker="$BABYLON_VALOPER")' | jq

# Check information of TX_HASH
babylond q tx <TX_HASH>

# Check how many blocks were passed by the validator and from which block the asset
babylond q slashing signing-info $(babylond tendermint show-validator)
```

```bash
Transactions

# Collect rewards from all validators delegated to them (without commission)
babylond tx distribution withdraw-all-rewards --from wallet --gas="auto" --gas-adjustment=1.2 --gas-prices="0.00001ubbn" -y

# Collect rewards from a separate validator or rewards + commission from your own validator
babylond tx distribution withdraw-rewards $BABYLON_VALOPER --from wallet --gas="auto" --gas-adjustment=1.2 --gas-prices="0.00001ubbn" --commission -y

# Delegate yourself (this is how 1 coin is sent)
babylond tx staking delegate $BABYLON_VALOPER 1000000ubbn --from wallet --gas="auto" --gas-adjustment=1.2 --gas-prices="0.00001ubbn" -y

# Redelegate to other validator
babylond tx staking redelegate $BABYLON_VALOPER <dst-validator-addr> 1000000ubbn --from wallet --gas="auto" --gas-adjustment=1.2 --gas-prices="0.00001ubbn" -y

# unbond 
babylond tx staking unbond $BABYLON_VALOPER 1000000ubbn --from wallet --gas="auto" --gas-adjustment=1.2 --gas-prices="0.00001ubbn" -y

# Send tokens to other adress
babylond tx bank send wallet <address> 1000000ubbn --gas="auto" --gas-adjustment=1.2 --gas-prices="0.00001ubbn" -y

# Escape from jail
babylond tx slashing unjail --from wallet --gas="auto" --gas-adjustment=1.2 --gas-prices="0.00001ubbn" -y
```

! If the transactions are not sent with the error account sequence mismatch, expected 18, got 17: incorrect account sequence, then add the flag -s 18 to the command (replace the number with the one that is waiting for the sequence)

## **Work with wallets**

```bash
# Chech the wallets list
babylond keys list

# Delete wallet
babylond keys delete <name_wallet>
```

## **Delete Node**

```bash
sudo systemctl stop babylond && \
sudo systemctl disable babylond && \
rm /etc/systemd/system/babylond.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf babylon && \
rm -rf .babylond && \
rm -rf $(which babylond)
```

## Governance

```bash
# List proposals
babylond q gov proposals

# Check voting result
babylond q gov proposals --voter $BABYLON_ADDRESS

# Vote 
babylond tx gov vote 20 yes --from wallet --gas="auto" --gas-adjustment=1.2 --gas-prices="0.00001ubbn" -y
```

## **Peers and RPC**

```bash
FOLDER=.babylond

# Check your peer
PORTR=$(grep -A 3 "\[p2p\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+") && \
echo $(babylond tendermint show-node-id)@$(curl ifconfig.me)$PORTR

# Check port of RPC
echo -e "\033[0;32m$(grep -A 3 "\[rpc\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+")\033[0m"

# Check peers quantity
PORT=
curl -s http://localhost:BABYLON_PORT/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr | split(":")[2])"' | wc -l
```
