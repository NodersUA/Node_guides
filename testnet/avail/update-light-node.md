# Update (Light node)

```bash
sudo systemctl stop avail_light
cd ~/avail-light && git fetch
git checkout v1.7.5
cargo build --release
cp target/release/avail-light /usr/local/bin/avail_light
avail_light --version
sudo systemctl restart avail_light && journalctl -u avail_light -f -o cat
```
