# Update (Linux)

```bash
cd $HOME/sui
git pull
git checkout testnet-0.31.0
cargo build --release -p sui-node 
sudo mv $HOME/sui/target/release/sui-node /usr/local/bin/
sudo systemctl restart suid
```
