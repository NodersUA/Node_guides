# Update

```bash
# Update to v3.3.0

cd $HOME && rm -rf gitopia
git clone https://github.com/gitopia/gitopia
cd gitopia
git checkout v3.3.0
make install

systemctl stop gitopiad
sudo cp ~/go/bin/gitopiad ~/.gitopia/cosmovisor/genesis/bin
sudo cp ~/go/bin/gitopiad /usr/local/bin/gitopiad
gitopiad version

systemctl restart gitopiad
journalctl -u gitopiad -f -o cat
```
