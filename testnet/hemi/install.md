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
curl -L -O https://github.com/hemilabs/heminetwork/releases/download/v0.5.0/heminetwork_v0.5.0_linux_amd64.tar.gz
mkdir -p hemi
tar --strip-components=1 -xzvf heminetwork_v0.5.0_linux_amd64.tar.gz -C hemi
```

```bash
cd ~/heminetwork/bin && ./keygen -secp256k1 -json -net="testnet" > ~/heminetwork/bin/popm-address.json
```

Get tokens from [faucet](https://coinfaucet.eu/en/btc-testnet/)

```bash
sudo tee /root/heminetwork/.env > /dev/null <<EOF
POPM_BTC_PRIVKEY=$(jq -r '.private_key' ~/heminetwork/bin/popm-address.json)
POPM_STATIC_FEE=200
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
ExecStart=/root/heminetwork/hemi/popmd
WorkingDirectory=/root/heminetwork/hemi/
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
