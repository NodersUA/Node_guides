**Update**

```bash
cd $HOME/realio-network
git fetch --all
git checkout v...
make install
sudo systemctl restart realio-networkd && sudo journalctl -u realio-networkd -f -o cat
```
