# ICN

```bash
PRIVAT_KEY=
```

```bash
# Create service file (One command)
sudo tee /etc/systemd/system/icn.service > /dev/null <<EOF
[Unit]
Description=Install ICN Console
After=network.target

[Service]
Type=oneshot
ExecStart=/bin/bash -c "curl -o- https://console.icn.global/downloads/install/start.sh | bash -s -- -p $PRIVAT_KEY"
TimeoutStartSec=0
RemainAfterExit=true

[Install]
WantedBy=multi-user.target
EOF
```

```bash
# Start the node
systemctl daemon-reload
systemctl enable icn
systemctl restart icn && journalctl -u icn -f -o cat
```
