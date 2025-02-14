# LayerEdge

### Automatic Installation

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/layeredge)
```

### Node

```bash
sudo apt-get update
sudo apt-get install -y libzmq3-dev libczmq-dev pkg-config
```

```bash
# Install Go
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/system/go)
```

```bash
git clone https://github.com/layer-edge/verification-layer-tester.git
cd verification-layer-tester
```

```bash
./build.sh
```

```bash
sudo tee /etc/systemd/system/layeredge.service > /dev/null <<EOF
[Unit]
Description=Verification Layer Tester Service
After=network.target

[Service]
Type=simple
User=root
ExecStart=/root/verification-layer-tester/run.sh
Restart=always
RestartSec=5s
WorkingDirectory=/root/verification-layer-tester
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
EOF
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable layeredge
sudo systemctl restart layeredge
```

```bash
journalctl -u layeredge -f -o cat
```
