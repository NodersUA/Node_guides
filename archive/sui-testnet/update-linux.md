# Update (Linux)

```bash
sudo systemctl stop suid
cd $HOME/sui
git fetch upstream
git reset --hard e4fe9f5ed5e240a718571f23bc75e12e60445288
cargo build --release --bin sui-node

mv /$HOME/sui/target/release/sui-node /usr/local/bin/

sudo systemctl restart suid
journalctl -u suid -f
```
