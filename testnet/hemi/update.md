# Update

```bash
systemctl stop popmd
cd heminetwork
curl -L -O https://github.com/hemilabs/heminetwork/releases/download/v0.5.0/heminetwork_v0.5.0_linux_amd64.tar.gz
mkdir -p hemi
tar --strip-components=1 -xzvf heminetwork_v0.5.0_linux_amd64.tar.gz -C hemi
rm /usr/local/bin/popmd
```

```bash
sed -i 's/^POPM_STATIC_FEE=.*/POPM_STATIC_FEE=200/' /root/heminetwork/.env
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
