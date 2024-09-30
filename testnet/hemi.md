# Hemi

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

```
cd ~/heminetwork/bin && ./keygen -secp256k1 -json -net="testnet" > ~/popm-address.json
```

Get tokens from [faucet](https://coinfaucet.eu/en/btc-testnet/)

```
export POPM_BTC_PRIVKEY=<private_key>
export POPM_STATIC_FEE=200
export POPM_BFG_URL=wss://testnet.rpc.hemi.network/v1/ws/public
./popmd
```

```
sudo tee /etc/systemd/system/popmd.service > /dev/null <<EOF
[Unit]
Description=Hemi Service
After=network.target

[Service]
Environment="POPM_BTC_PRIVKEY=<private_key>"
Environment="POPM_STATIC_FEE=200"
Environment="POPM_BFG_URL=wss://testnet.rpc.hemi.network/v1/ws/public"
Type=simple
ExecStart=$(which popmd) run --POPM_BTC_PRIVKEY 
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF
```

```
# Start the node
systemctl daemon-reload
systemctl enable popmd
systemctl restart popmd && journalctl -u popmd -f -o cat
```
