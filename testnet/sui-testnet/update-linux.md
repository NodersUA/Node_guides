# Update (Linux)

```bash
sudo systemctl stop suid
cd $HOME/sui
git fetch upstream
git reset --hard c525ba6489261ff6db65e87bf9a3fdda0a6c7be3
cargo build --release --bin sui-node

mv /$HOME/sui/target/release/sui-node /usr/local/bin/

sudo systemctl restart suid
journalctl -u suid -f
```
