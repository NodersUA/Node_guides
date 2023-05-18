**Snapshot**

```bash
sudo systemctl stop gitopiad

cp $HOME/.gitopia/data/priv_validator_state.json $HOME/.gitopia/priv_validator_state.json.backup 

gitopiad tendermint unsafe-reset-all --home $HOME/.gitopia --keep-addr-book 
curl https://snapshots1-testnet.nodejumper.io/gitopia-testnet/gitopia-janus-testnet-2_2023-04-23.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.gitopia

mv $HOME/.gitopia/priv_validator_state.json.backup $HOME/.gitopia/data/priv_validator_state.json 

sudo systemctl restart gitopiad
sudo journalctl -u gitopiad -f --no-hostname -o cat
```

***State Sync***
```bash
sudo systemctl stop gitopiad

cp $HOME/.gitopia/data/priv_validator_state.json $HOME/.gitopia/priv_validator_state.json.backup
gitopiad tendermint unsafe-reset-all --home $HOME/.gitopia --keep-addr-book

SNAP_RPC="https://gitopia-testnet.nodejumper.io:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height)
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000))
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

PEERS="6146658ffe2d148524a9fdcc3d701440053442bf@gitopia-testnet.nodejumper.io:30656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.gitopia/config/config.toml

sed -i 's|^enable *=.*|enable = true|' $HOME/.gitopia/config/config.toml
sed -i 's|^rpc_servers *=.*|rpc_servers = "'$SNAP_RPC,$SNAP_RPC'"|' $HOME/.gitopia/config/config.toml
sed -i 's|^trust_height *=.*|trust_height = '$BLOCK_HEIGHT'|' $HOME/.gitopia/config/config.toml
sed -i 's|^trust_hash *=.*|trust_hash = "'$TRUST_HASH'"|' $HOME/.gitopia/config/config.toml

mv $HOME/.gitopia/priv_validator_state.json.backup $HOME/.gitopia/data/priv_validator_state.json

sudo systemctl restart gitopiad
sudo journalctl -u gitopiad -f --no-hostname -o cat
```

***AddrBook***

```bash
curl -s https://snapshots1-testnet.nodejumper.io/gitopia-testnet/addrbook.json > $HOME/.gitopia/config/addrbook.json

systemctl restart gitopiad && journalctl -u gitopiad -f -o cat
```
