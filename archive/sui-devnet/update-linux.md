# Update (Linux)

```bash
systemctl stop suid && \
rm -rf $HOME/.sui/db && \
wget -qO $HOME/.sui/genesis.blob https://github.com/MystenLabs/sui-genesis/raw/main/devnet/genesis.blob && \
cd $HOME/sui && \
git fetch upstream && \
git stash && \
git checkout -B devnet --track upstream/devnet && \
cargo build --release && \
mv $HOME/sui/target/release/{sui,sui-node,sui-faucet} /usr/bin/ && \
sui -V
```

```bash
# Check your version, should be sui 0.29...
```

```bash
cp crates/sui-config/data/fullnode-template.yaml fullnode.yaml && \
systemctl restart suid && sudo journalctl -fn 100 -u suid
```
