# Pipe

### Automatic Installation

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/pipe)
```

### Node

```bash
# download the compiled pop binary
curl -L -o /usr/local/bin/pop "https://dl.pipecdn.app/v0.2.5/pop"
chmod +x /usr/local/bin/pop
mkdir -p /var/cache/pop/download_cache
mkdir -p /var/lib/pop
```

```
SOL_ADDRESS=
```

```bash
# Create systemd service file
sudo tee /etc/systemd/system/pop.service > /dev/null <<EOF
[Unit]
Description=Pipe POP Node Service
After=network.target
Wants=network-online.target

[Service]
User=root
Group=root
ExecStart=/usr/local/bin/pop \
    --ram=12 \
    --pubKey $SOL_ADDRESS \
    --max-disk 500 \
    --cache-dir /var/cache/pop/download_cache \
    --no-prompt
Restart=always
RestartSec=5
LimitNOFILE=65536
LimitNPROC=4096
StandardOutput=journal
StandardError=journal
SyslogIdentifier=pop-node
WorkingDirectory=/var/lib/pop

[Install]
WantedBy=multi-user.target
EOF
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable pop.service
sudo systemctl start pop.service
```

```bash
journalctl -u pop -f -o cat
```
