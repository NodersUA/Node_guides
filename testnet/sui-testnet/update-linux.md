# Update (Linux)

```bash
sudo systemctl stop suid
cd $HOME/sui
git fetch upstream
git reset --hard 133fd85bcd79ed048a897be3a3f3bd4a2e9c616a
cargo build --release --bin sui-node

mv /$HOME/sui/target/release/sui-node /usr/local/bin/

sudo systemctl restart suid
journalctl -u suid -f
```
