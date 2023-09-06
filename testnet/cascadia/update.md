# Update

```bash
systemctl stop cascadiad
cd /usr/local/bin/ && rm cascadiad
curl -L https://github.com/CascadiaFoundation/cascadia/releases/download/v0.1.5/cascadiad -o cascadiad
chmod +x cascadiad && cd

sudo tee /etc/systemd/system/cascadiad.service > /dev/null <<EOF
[Unit]
Description=Cascadia Node
After=network.target
 
[Service]
Type=simple
User=$USER
WorkingDirectory=$HOME/go/bin
ExecStart=/usr/local/bin/cascadiad start --trace --log_level info --json-rpc.api eth,txpool,personal,net,debug,web3 --api.enable --chain-id cascadia_6102-1
Restart=on-failure
StartLimitInterval=0
RestartSec=3
LimitNOFILE=65535
LimitMEMLOCK=209715200
 
[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl restart cascadiad
journalctl -u cascadiad -f --no-hostname -o cat
```
