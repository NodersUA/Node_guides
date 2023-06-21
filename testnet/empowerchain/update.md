# Update

```bash
systemctl stop empowerd
cd ~/empowerchain
git fetch
git checkout v1.0.0-rc3
cd chain && make install

sudo cp ~/go/bin/empowerd /usr/local/bin/
empowerd version --long | grep -e version -e commit
systemctl restart empowerd
journalctl -u empowerd -f --no-hostname -o cat
```
