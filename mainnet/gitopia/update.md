# Update

```bash
# Update to v3.0.1

cd $HOME && rm -rf gitopia
git clone https://github.com/gitopia/gitopia.git
git checkout v3.0.1
cd gitopia && make build

systemctl stop gitopiad
sudo cp ~/go/bin/gitopiad ~/.gitopia/cosmovisor/genesis/bin
sudo cp ~/go/bin/gitopiad /usr/local/bin/gitopiad
gitopiad version

systemctl restart gitopiad
journalctl -u gitopiad -f -o cat
```
