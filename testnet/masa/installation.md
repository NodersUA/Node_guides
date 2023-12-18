# Installation

```bash
# Update the repositories
apt update && apt upgrade -y
```

```bash
# Install Go (one command)
if [ "$(go version)" != "go version go1.20.2 linux/amd64" ]; then \
ver="1.21.3" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile ; \
fi

go version

# go version go1.21.3 linux/amd64
```

```bash
git clone https://github.com/masa-finance/masa-oracle-go-testnet.git masa
cd masa
go build -v -o masa-node ./cmd/masa-node

sudo cp masa-node /usr/local/bin/ && cd $HOME
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
