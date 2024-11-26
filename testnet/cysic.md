# Cysic

### Automatic Installation

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/cysic)
```

### Node

```bash
sudo apt update && sudo apt upgrade -y
```

```bash
MM=<your evm address>
```

```bash
curl -L https://github.com/cysic-labs/phase2_libs/releases/download/v1.0.0/setup_linux.sh > ~/setup_linux.sh && bash ~/setup_linux.sh $MM
```

```bash
chmod +x ~/cysic-verifier/start.sh
```

```bash
sudo tee /etc/systemd/system/cysic_verifier.service > /dev/null <<EOF
[Unit]
Description=Cysic Verifier Service
After=network.target

[Service]
Type=simple
User=root
ExecStart=/bin/bash /root/cysic-verifier/start.sh
Restart=always
RestartSec=5
WorkingDirectory=/root/cysic-verifier

[Install]
WantedBy=multi-user.target

EOF
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable cysic_verifier.service
sudo systemctl restart cysic_verifier.service
```

```bash
journalctl -u cysic_verifier -f -o cat
```
