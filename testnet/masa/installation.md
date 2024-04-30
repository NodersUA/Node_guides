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
git clone git@github.com:masa-finance/masa-oracle.git masa
cd masa
go build -v -o masa-node ./cmd/masa-node
sudo cp masa-node /usr/local/bin/
```

```bash
sudo tee $HOME/masa/.env > /dev/null <<EOF
BOOTNODES=/ip4/35.223.224.220/udp/4001/quic-v1/p2p/16Uiu2HAmPxXXjR1XJEwckh6q1UStheMmGaGe8fyXdeRs3SejadSa
RPC_URL=https://ethereum-sepolia.publicnode.com
ENV=test
FILE_PATH=.
WRITER_NODE=false
TWITTER_SCRAPER=false
WEB_SCRAPER=false
PORT=8282
EOF
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
    ./masa-node --stake 1000
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
ExecStart=/usr/local/bin/masa-node
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
