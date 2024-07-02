# Update

```bash
su lgtn
```

```bash
cd ~/fleek-network/lightning

git checkout testnet-alpha-1

git stash 

git pull origin testnet-alpha-1

git fetch origin testnet-alpha-1
git reset --hard origin/testnet-alpha-1
git clean -f

cargo +stable build --release
```

```bash
sudo rm -f "/usr/local/bin/lgtn"
sudo ln -s ~/fleek-network/lightning/target/release/lightning-node /usr/local/bin/lgtn

sed -i "s|~/.lightning|/home/lgtn/.lightning|g" "/home/lgtn/.lightning/config.toml"

sudo rm -rf ~/.lightning/data

sudo systemctl restart lightning
```

```bash
# Check node health
curl -sS https://get.fleek.network/healthcheck | bash
```
