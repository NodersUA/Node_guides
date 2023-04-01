**Snapshot**

```bash
curl -L https://snapshots.kjnodes.com/gitopia-testnet/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.gitopia
```

***State Sync***
```bash

SNAP_RPC=https://t-gitopia.rpc.utsa.tech:443

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.gitopia/config/config.toml
wget -O $HOME/.gitopia/config/addrbook.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/Gitopia/addrbook.json"
```

***AddrBook***

```bash
wget -O $HOME/.gitopia/config/addrbook.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/Gitopia/addrbook.json"

systemctl restart gitopiad && journalctl -u gitopiad -f -o cat
