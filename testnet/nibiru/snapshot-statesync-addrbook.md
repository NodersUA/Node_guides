***Sanpshot***t
```bash
snapshot_interval=1000
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/" ~/.nibid/config/app.toml
```

***State Sync***
```bash
# install lz4
apt update
apt install snapd -y
snap install lz4
# скачиваем wasm
curl -L https://share.utsa.tech/nibiru/wasm-nibiru.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.nibid --strip-components 2
# добавляем peer
peers="d9699de67d7038f2f47ec60e235b146ee98a6936@65.108.6.45:60656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.nibid/config/config.toml
SNAP_RPC=https://t-nibiru.rpc.utsa.tech:443

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.nibid/config/config.toml

systemctl restart nibid && journalctl -u nibid -f -o cat
```
***If you cannot connect to peers long time download addrbook***
```bash
wget -O $HOME/.nibid/config/addrbook.json "https://share.utsa.tech/nibiru/addrbook.json"

systemctl restart nibid && journalctl -u nibid -f -o cat
```
