# Install

```bash
curl -sL1 https://nubit.sh | bash
```

```bash
sudo tee /etc/systemd/system/nubit.service > /dev/null <<EOF
[Unit]
Description=Nubit Install Script Service
After=network.target

[Service]
Type=simple
ExecStart=/bin/bash -c 'export HOME=/root && curl -sL1 https://nubit.sh | bash'
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF
```

```bash
# Start the node
systemctl daemon-reload
systemctl enable nubit
systemctl restart nubit && journalctl -u nubit -f -o cat
```
