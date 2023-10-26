**SnapShot**
```bash
snapshot_interval=1000
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/" ~/.andromedad/config/app.toml

# install lz4
apt update
apt install snapd -y
snap install lz4

udo systemctl stop andromedad

cp $HOME/.andromedad/data/priv_validator_state.json $HOME/.andromedad/priv_validator_state.json.backup 

andromedad tendermint unsafe-reset-all --home $HOME/.andromedad --keep-addr-book 
curl https://snapshots2-testnet.nodejumper.io/andromeda-testnet/galileo-3_2023-04-23.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.andromedad

mv $HOME/.andromedad/priv_validator_state.json.backup $HOME/.andromedad/data/priv_validator_state.json 

sudo systemctl restart andromedad
sudo journalctl -u andromedad -f --no-hostname -o cat
```

**State Sync**
```bash
sudo systemctl stop andromedad

cp $HOME/.andromedad/data/priv_validator_state.json $HOME/.andromedad/priv_validator_state.json.backup
andromedad tendermint unsafe-reset-all --home $HOME/.andromedad --keep-addr-book

SNAP_RPC="https://andromeda-testnet.nodejumper.io:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height)
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000))
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

PEERS="9d058b4c4eb63122dfab2278d8be1bf6bf07f9ef@andromeda-testnet.nodejumper.io:26656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.andromedad/config/config.toml

sed -i 's|^enable *=.*|enable = true|' $HOME/.andromedad/config/config.toml
sed -i 's|^rpc_servers *=.*|rpc_servers = "'$SNAP_RPC,$SNAP_RPC'"|' $HOME/.andromedad/config/config.toml
sed -i 's|^trust_height *=.*|trust_height = '$BLOCK_HEIGHT'|' $HOME/.andromedad/config/config.toml
sed -i 's|^trust_hash *=.*|trust_hash = "'$TRUST_HASH'"|' $HOME/.andromedad/config/config.toml

mv $HOME/.andromedad/priv_validator_state.json.backup $HOME/.andromedad/data/priv_validator_state.json

curl -s https://snapshots2-testnet.nodejumper.io/andromeda-testnet/wasm.lz4 | lz4 -dc - | tar -xf - -C $HOME/.andromedad

sudo systemctl restart andromedad
sudo journalctl -u andromedad -f --no-hostname -o cat
```

***If you cannot connect to peers long time download addrbook***
```bash
curl -s https://snapshots2-testnet.nodejumper.io/andromeda-testnet/addrbook.json > $HOME/.andromedad/config/addrbook.json

systemctl restart andromedad && journalctl -u andromedad -f -o cat
```
