# Update (Linux)

```bash
sudo systemctl stop suid
cd $HOME/sui
git fetch upstream
git reset --hard 09b2081498366df936abae26eea4b2d5cafb2788
cargo build --release --bin sui-node

mv /$HOME/sui/target/release/sui-node /usr/local/bin/

sudo systemctl restart suid
journalctl -u suid -f
```
