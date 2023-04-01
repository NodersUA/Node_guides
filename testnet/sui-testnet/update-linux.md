# Update (Linux)

```bash
sudo systemctl stop suid && \
cd $HOME/sui && \
git fetch upstream && \
git checkout -B testnet --track upstream/testnet && \
cargo build --release -p sui-node -p sui && \
mv $HOME/sui/target/release/sui-node /usr/local/bin/ && \
mv $HOME/sui/target/release/sui /usr/local/bin/ && \
sudo systemctl restart suid && \
sudo journalctl -u suid -f
```
