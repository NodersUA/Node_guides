***SnapShot***
```bash
snapshot_interval=1000
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/" ~/.sao/config/app.toml

# Snapshot
systemctl stop saod

cp $HOME/.sao/data/priv_validator_state.json $HOME/.sao/priv_validator_state.json.backup
saod tendermint unsafe-reset-all --home $HOME/.sao --keep-addr-book

# download wasm if necessary
#curl -L https://share.utsa.tech/sao/wasm-sao.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.sao --strip-components 2
# download snapshot (data)
#curl -L https://share2.utsa.tech/sao/snap-sao.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.sao --strip-components 2
SNAP_NAME=$(curl -s https://ss-t.sao.nodestake.top/ | egrep -o ">20.*\.tar.lz4" | tr -d ">")
curl -o - -L https://ss-t.sao.nodestake.top/${SNAP_NAME}  | lz4 -c -d - | tar -x -C $HOME/.sao

mv $HOME/.sao/priv_validator_state.json.backup $HOME/.sao/data/priv_validator_state.json

sudo systemctl restart saod && journalctl -u saod -f -o cat
```

***State Sync***
```bash
SNAP_RPC=

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 100)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.sao/config/config.toml

systemctl restart saod && journalctl -u saod -f -o cat
```

***If you cannot connect to peers long time download addrbook***
```bash
# AddrBook
wget -O $HOME/.sao/config/addrbook.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/Sao/addrbook.json"
# Restart
sudo systemctl restart saod && sudo journalctl -u saod -f
```
