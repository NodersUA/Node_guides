# StateSync/Snapshot/AddrBook

SnapShot

```bash
snapshot_interval=1000
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/" ~/.lava/config/app.toml
sudo systemctl stop lavad

cp $HOME/.lava/data/priv_validator_state.json $HOME/.lava/priv_validator_state.json.backup 

lavad tendermint unsafe-reset-all --home $HOME/.lava --keep-addr-book 
curl https://snapshots1-testnet.nodejumper.io/lava-testnet/ | lz4 -dc - | tar -xf - -C $HOME/.lava

mv $HOME/.lava/priv_validator_state.json.backup $HOME/.lava/data/priv_validator_state.json 

sudo systemctl restart lavad
sudo journalctl -u lavad -f --no-hostname -o cat
```

**StateSync**

```bash
lavad tendermint unsafe-reset-all --home $HOME/.lava --keep-addr-book

SNAP_NAME=$(curl -s https://snapshots1-testnet.nodejumper.io/lava-testnet/info.json | jq -r .fileName)
curl "https://snapshots1-testnet.nodejumper.io/lava-testnet/${SNAP_NAME}" | lz4 -dc - | tar -xf - -C "$HOME/.lava"

```

If you cannot connect to peers long time download addrbook.

```bash
curl -s https://snapshots1-testnet.nodejumper.io/lava-testnet/addrbook.json > $HOME/.lava/config/addrbook.json

systemctl restart lavad && journalctl -u lavad -f -o cat
```
