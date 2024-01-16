# Installation

```bash
# Update the repositories
apt update && apt upgrade -y
```

```bash
# Install Go
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/system/go)
```

```bash
git clone https://github.com/masa-finance/masa-oracle-go-testnet.git masa
cd masa
go build -v -o masa-node ./cmd/masa-node

sudo cp masa-node /usr/local/bin/
```

### Staking Tokens&#x20;

To participate in the network and earn rewards, you must first stake your tokens:

1.  Start your node to get Private Key

    ```
    ./masa-node --start
    ```
2.  Get your private key from the node

    ```
    cat /root/.masa/masa_oracle_key.ecdsa
    ```
3. Import the private key into Metamask to access your Ethereum address.
4.  Send Sepolia ETH and Masa testnet tokens to your address. Then stake!:

    ```
    ./masa-node --stake 100
    ```

```bash
# Create service file (One command)
sudo tee /etc/systemd/system/masa.service > /dev/null <<EOF
[Unit]
Description=Masa Node
After=network.target
 
[Service]
Type=simple
User=$USER
WorkingDirectory=$HOME/masa/
ExecStart=/usr/local/bin/masa-node --port=8282 --udp=true --tcp=false --start=true
Restart=on-failure
StartLimitInterval=0
RestartSec=3
LimitNOFILE=65535
LimitMEMLOCK=209715200
 
[Install]
WantedBy=multi-user.target
EOF
```

```bash
# Start the node
systemctl daemon-reload
systemctl enable masa
systemctl restart masa && journalctl -u masa -f -o cat

# Escape from logs ctrl+c
```
