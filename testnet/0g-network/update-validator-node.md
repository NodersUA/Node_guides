# Update (Validator Node)

```bash
systemctl stop Ogchaind
rm /usr/local/bin/0gchaind
cd 0g-chain/
git reset --hard HEAD
git fetch && git checkout v0.3.1
git submodule update --init
make install

sudo cp $(which 0gchaind) /usr/local/bin/ && cd $HOME
0gchaind version --long | grep -e version -e commit
```

```bash
wget https://server-5.itrocket.net/testnet/og/genesis.json -O $HOME/.0gchain/config/genesis.json
```

```bash
# Create service file (One command)
sudo tee /etc/systemd/system/Ogchaind.service > /dev/null <<EOF
[Unit]
Description=0G Node
After=network.target
 
[Service]
Type=simple
User=$USER
WorkingDirectory=$HOME/go/bin
ExecStart=/usr/local/bin/0gchaind start --home $HOME/.0gchain --log_output_console
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
systemctl enable Ogchaind
systemctl restart Ogchaind && journalctl -u Ogchaind -f -o cat

# Escape from logs ctrl+c
```
