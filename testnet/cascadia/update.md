# Update

```bash
systemctl stop cascadiad
cd ~/cascadia
git fetch
git checkout v0.1.2
make install
systemctl restart cascadiad
journalctl -u cascadiad -f --no-hostname -o cat
```
