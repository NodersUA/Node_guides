# Install

## _**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/hemi)
```

## **Manual Installation**

```bash
sudo apt update
```

```bash
# Install Go
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/system/go)
```

```bash
git clone https://github.com/hemilabs/heminetwork.git
cd heminetwork
make deps
make install
cp popmd /usr/local/bin/
```

```bash
cd ~/heminetwork/bin && ./keygen -secp256k1 -json -net="testnet" > ~/heminetwork/bin/popm-address.json
```

Get tokens from [faucet](https://coinfaucet.eu/en/btc-testnet/)

```bash
sudo tee /root/heminetwork/.env > /dev/null <<EOF
POPM_BTC_PRIVKEY=$(jq -r '.private_key' ~/heminetwork/bin/popm-address.json)
POPM_STATIC_FEE=20
POPM_BFG_URL=wss://testnet.rpc.hemi.network/v1/ws/public
EOF
```

```bash
sudo tee /etc/systemd/system/popmd.service > /dev/null <<EOF
[Unit]
Description=Hemi Service
After=network.target

[Service]
User=root
Type=simple
EnvironmentFile=/root/heminetwork/.env
ExecStart=/usr/local/bin/popmd
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF
```

```bash
# Start the node
systemctl daemon-reload
systemctl enable popmd
systemctl restart popmd && journalctl -u popmd -f -o cat
```
