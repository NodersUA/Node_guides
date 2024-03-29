## Automatic Installation
```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/gear)
```

## Manual Installation


```bash
# Update the repositories
apt update && apt upgrade -y
```

```bash
# Open ports
ufw allow 30333
```

```bash
# Set the variables

# Come up with the name of your node and replace it instead <your_moniker>
GEAR_MONIKER=<your_moniker>
echo 'export GEAR_MONIKER='$GEAR_MONIKER >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
curl https://get.gear.rs/gear-v0.3.1-x86_64-unknown-linux-gnu.tar.xz | sudo tar -xJC /usr/bin
```

```bash
/usr/bin/gear --version

# gear 0.3.1-5d8fb07221d
```

```bash
# Create service file (One command)
sudo tee <<EOF >/dev/null /etc/systemd/system/gear-node.service
[Unit]
Description=Gear Node
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/root/
ExecStart=/usr/bin/gear --name "$GEAR_MONIKER" --telemetry-url "ws://telemetry-backend-shard.gear-tech.io:32001/submit 0"
Restart=always
RestartSec=3
LimitNOFILE=10000

[Install]
WantedBy=multi-user.target
EOF
```

```bash
# Start the node
systemctl daemon-reload
systemctl enable nibid
systemctl restart gear-node && journalctl -u gear-node -f -o cat

# Escape from logs ctrl+c
```

```bash
# Check the logs
journalctl -u gear-node -f -o cat
```

Check your node in [telemetry](https://telemetry.gear-tech.io/)
