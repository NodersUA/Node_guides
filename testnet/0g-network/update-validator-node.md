# Update (Validator Node)

```bash
systemctl stop Ogchaind
rm /usr/local/bin/0gchaind
cd 0g-chain/
git reset --hard HEAD
git fetch && git checkout v0.5.0
git submodule update --init
make install

sudo cp $(which 0gchaind) /usr/local/bin/ && cd $HOME
0gchaind version --long | grep -e version -e commit
```

```bash
systemctl restart Ogchaind && journalctl -u Ogchaind -f -o cat
```
