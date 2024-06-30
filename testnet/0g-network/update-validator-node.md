# Update (Validator Node)

```bash
systemctl stop Ogchaind
rm /usr/local/bin/0gchaind
cd 0g-chain/
git reset --hard HEAD
git fetch && git checkout v0.2.3
make install

sudo cp $(which 0gchaind) /usr/local/bin/ && cd $HOME
0gchaind version --long | grep -e version -e commit
```

```bash
rm $HOME/.0gchain/config/addrbook.json $HOME/.0gchain/config/genesis.json
0gchaind tendermint unsafe-reset-all --home $HOME/.0gchain
```

```bash
echo "export OG_CHAIN_ID=zgtendermint_16600-2" >> $HOME/.bash_profile
source $HOME/.bash_profile
0gchaind config chain-id $OG_CHAIN_ID
```

```bash
wget https://github.com/0glabs/0g-chain/releases/download/v0.2.3/genesis.json -O $HOME/.0gchain/config/genesis.json
```

```bash
PEERS=$(curl -sS https://0grpc.tech-coha05.xyz/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}' | paste -sd, -)
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.0gchain/config/config.toml
```

```bash
if ! dpkg -s lz4 &> /dev/null; then
sudo apt update && apt upgrade -y
apt install lz4 -y
fi

cp $HOME/.0gchain/data/priv_validator_state.json $HOME/.0gchain/priv_validator_state.json.backup

0gchaind tendermint unsafe-reset-all --home $HOME/.0gchain --keep-addr-book
curl https://snapshots-testnet.nodejumper.io/0g-testnet/0g-testnet_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.0gchain

mv $HOME/.0gchain/priv_validator_state.json.backup $HOME/.0gchain/data/priv_validator_state.json

sudo systemctl restart 0gchaind
sudo journalctl -u 0gchaind -f --no-hostname -o cat
```
