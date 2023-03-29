**Update**

```bash
cd $HOME/realio-network
git fetch --all
git checkout v0.8.0-rc3
make install
sudo systemctl restart realio-networkd && sudo journalctl -u realio-networkd -f -o cat
```

```bash
# Check version
realio-networkd version

# v0.8.0-rc3
```
