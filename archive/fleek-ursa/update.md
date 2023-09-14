# Update

```bash
sudo systemctl disable fleekd
sudo systemctl stop fleekd

if [ ! -d "${HOME}/ursa" ]; then
  git clone https://github.com/fleek-network/ursa.git
fi

cd $HOME/ursa && git pull origin main

sudo systemctl restart fleekd && \
sudo journalctl -u fleekd -f -o cat
```
