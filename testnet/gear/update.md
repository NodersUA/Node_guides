# Update

```bash
# gear 0.2.2-cc0235a33b1

curl https://get.gear.rs/gear-nightly-x86_64-unknown-linux-gnu.tar.xz | sudo tar -xJC /usr/bin
sudo systemctl restart gear-node
/usr/bin/gear --version
sudo journalctl -fu gear-node --no-hostname -o cat
```
