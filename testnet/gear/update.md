# Update

```bash
# gear 0.3.1-5d8fb07221d

sudo systemctl stop gear-node
gear purge-chain -y
curl https://get.gear.rs/gear-v0.3.1-x86_64-unknown-linux-gnu.tar.xz | sudo tar -xJC /usr/bin
sudo systemctl restart gear-node
/usr/bin/gear --version
sudo journalctl -fu gear-node --no-hostname -o cat
```
