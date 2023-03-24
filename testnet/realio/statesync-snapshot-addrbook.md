***Sanpshot***

```bash
snapshot_interval=1000 && \
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/" ~/.realio-network/config/app.toml
```

***State Sync***

```bash
sudo systemctl stop realio-networkd
cp $HOME/.realio-network/data/priv_validator_state.json $HOME/.realio-network/priv_validator_state.json.backup
realio-networkd tendermint unsafe-reset-all --home $HOME/.realio-network

STATE_SYNC_RPC=http://rpc.realio.ppnv.space:16657
STATE_SYNC_PEER=d0de51b7de393935cdc29dda114431964be93090@rpc.realio.ppnv.space:16656

LATEST_HEIGHT=$(curl -s $STATE_SYNC_RPC/block | jq -r .result.block.header.height)
SYNC_BLOCK_HEIGHT=$(($LATEST_HEIGHT - 1000))
SYNC_BLOCK_HASH=$(curl -s "$STATE_SYNC_RPC/block?height=$SYNC_BLOCK_HEIGHT" | jq -r .result.block_id.hash)

sed -i.bak -e "s|^enable *=.*|enable = true|" $HOME/.realio-network/config/config.toml

sed -i.bak -e "s|^rpc_servers *=.*|rpc_servers = \"$STATE_SYNC_RPC,$STATE_SYNC_RPC\"|" $HOME/.realio-network/config/config.toml

sed -i.bak -e "s|^trust_height *=.*|trust_height = $SYNC_BLOCK_HEIGHT|" $HOME/.realio-network/config/config.toml
sed -i.bak -e "s|^trust_hash *=.*|trust_hash = \"$SYNC_BLOCK_HASH\"|" $HOME/.realio-network/config/config.toml
sed -i.bak -e "s|^persistent_peers *=.*|persistent_peers = \"$STATE_SYNC_PEER\"|" $HOME/.realio-network/config/config.toml

mv $HOME/.realio-network/priv_validator_state.json.backup $HOME/.realio-network/data/priv_validator_state.json
sudo systemctl restart realio-networkd && journalctl -u realio-networkd -f --no-hostname -o cat
```

