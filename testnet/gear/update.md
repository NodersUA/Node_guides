# Update

```bash
wget https://get.gear.rs/gear-nightly-linux-x86_64.tar.xz
sudo tar -xvf gear-nightly-linux-x86_64.tar.xz -C /usr/bin
rm gear-nightly-linux-x86_64.tar.xz
sudo systemctl restart gear-node
sudo journalctl -fu gear-node --no-hostname -o cat
```
