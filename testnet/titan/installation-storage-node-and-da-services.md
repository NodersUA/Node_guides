# Installation (Storage Node And DA Services)

```bash
sudo apt-get update
sudo apt-get install clang cmake build-essential
```

```bash
# Install Go
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/system/go)
```

```bash
git clone -b v0.5.1 https://github.com/0glabs/0g-storage-node.git
cd 0g-storage-node
git submodule update --init

# Build in release mode
cargo build --release
```

```bash
cp run/config.toml run/config_temp.toml


```

```bash
# Create service file (One command)
sudo tee /etc/systemd/system/0gstorage.service > /dev/null <<EOF
[Unit]
Description=0G Storage Node
After=network.target
 
[Service]
Type=simple
User=$USER
ExecStart=/root/0g-storage-node/target/release/zgs_node --config config.toml
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
systemctl enable 0gstorage
systemctl restart 0gstorage && journalctl -u 0gstorage -f -o cat

# Escape from logs ctrl+c
```
