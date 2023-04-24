***SnapShot***
```bash
snapshot_interval=1000
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/" ~/.dymension/config/app.toml

sudo systemctl stop ojod

cp $HOME/.ojo/data/priv_validator_state.json $HOME/.ojo/priv_validator_state.json.backup 

ojod tendermint unsafe-reset-all --home $HOME/.ojo --keep-addr-book 
curl https://snapshots2-testnet.nodejumper.io/ojo-testnet/null_2023-04-24.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.ojo

mv $HOME/.ojo/priv_validator_state.json.backup $HOME/.ojo/data/priv_validator_state.json 

sudo systemctl restart ojod
sudo journalctl -u ojod -f --no-hostname -o cat
```

***State Sync***
```bash
sudo systemctl stop ojod

cp $HOME/.ojo/data/priv_validator_state.json $HOME/.ojo/priv_validator_state.json.backup
ojod tendermint unsafe-reset-all --home $HOME/.ojo --keep-addr-book

SNAP_RPC="https://ojo-testnet.nodejumper.io:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height)
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000))
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

PEERS="17a5fad48064ee3da42f435925f7bbe055e6348d@ojo-testnet.nodejumper.io:37656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.ojo/config/config.toml

sed -i 's|^enable *=.*|enable = true|' $HOME/.ojo/config/config.toml
sed -i 's|^rpc_servers *=.*|rpc_servers = "'$SNAP_RPC,$SNAP_RPC'"|' $HOME/.ojo/config/config.toml
sed -i 's|^trust_height *=.*|trust_height = '$BLOCK_HEIGHT'|' $HOME/.ojo/config/config.toml
sed -i 's|^trust_hash *=.*|trust_hash = "'$TRUST_HASH'"|' $HOME/.ojo/config/config.toml

mv $HOME/.ojo/priv_validator_state.json.backup $HOME/.ojo/data/priv_validator_state.json

sudo systemctl restart ojod
sudo journalctl -u ojod -f --no-hostname -o cat
```

***If you cannot connect to peers long time download addrbook***
```bash
# AddrBook
curl -s https://snapshots2-testnet.nodejumper.io/ojo-testnet/addrbook.json > $HOME/.ojo/config/addrbook.json

sudo systemctl restart ojod && sudo journalctl -u ojod -f
```
