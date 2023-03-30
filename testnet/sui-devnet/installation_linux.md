# Installation (Linux)

### Automatic Installation

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/sui_autoinstall.sh)
```

### Manual

```bash
# Update the repositories and developer packages
sudo apt-get update \
&& sudo apt-get install -y --no-install-recommends \
tzdata \
ca-certificates \
build-essential \
libssl-dev \
libclang-dev \
pkg-config \
cmake
```

```bash
# Install Rust
sudo curl https://sh.rustup.rs -sSf | sh -s -- -y
source $HOME/.cargo/env
```

```bash
# Create directory for SUI db and genesis state file
mkdir -p /var/sui/db
```

```bash
# Clone GitHub SUI repository
cd $HOME
git clone https://github.com/MystenLabs/sui.git
cd sui
git remote add upstream https://github.com/MystenLabs/sui
git fetch upstream
git checkout --track upstream/devnet
```

```bash
# Make a copy of fullnode.yaml and update path to db and genesis state file.
cp crates/sui-config/data/fullnode-template.yaml /var/sui/fullnode.yaml
sed -i.bak "s/db-path:.*/db-path: \"\/var\/sui\/db\"/ ; s/genesis-file-location:.*/genesis-file-location: \"\/var\/sui\/genesis.blob\"/" /var/sui/fullnode.yaml
```

```bash
# Download Genesis
wget -P /var/sui https://github.com/MystenLabs/sui-genesis/raw/main/devnet/genesis.blob
```

```bash
# Build SUI binaries
cargo build --release
mv ~/sui/target/release/sui-node /usr/local/bin/
mv ~/sui/target/release/sui /usr/local/bin/

```

```bash
# Create Service file for SUI Node
echo "[Unit]
Description=Sui Node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=/usr/local/bin/sui-node --config-path /var/sui/fullnode.yaml
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > $HOME/suid.service

mv $HOME/suid.service /etc/systemd/system/

sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
```

```bash
# Start the node
sudo systemctl restart systemd-journald
sudo systemctl daemon-reload
sudo systemctl enable suid
sudo systemctl start suid
sudo journalctl -fn 100 -u suid
```
