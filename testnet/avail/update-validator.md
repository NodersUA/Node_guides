# Update (Validator)

```bash
systemctl stop availd
cd ~/avail && git fetch
git checkout v1.10.0.0
cargo build --release -p data-avail
cp target/release/data-avail /usr/local/bin/avail
avail --version
systemctl restart availd && journalctl -u availd -f -o cat
```
