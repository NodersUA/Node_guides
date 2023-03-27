# Installation

### Automatic Installation

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/sui_autoinstall.sh)
```

### Manual

```bash
# Update the repositories
apt update && apt upgrade -y
```

```bash
# Install developer packages
sudo apt install wget jq git libclang-dev libpq-dev cmake -y
```

```bash
# Install Rust
. <(wget -qO- https://raw.githubusercontent.com/SecorD0/utils/main/installers/rust.sh)

```

```bash
# Download binary files (One command)
mkdir -p $HOME/.sui
git clone https://github.com/MystenLabs/sui && \
cd sui && \
git remote add upstream https://github.com/MystenLabs/sui && \
git fetch upstream && \
git checkout -B devnet --track upstream/devnet && \
cargo build --release && \
mv $HOME/sui/target/release/{sui,sui-node,sui-faucet} /usr/bin/ && cd
```

```bash
# Download Genesis
wget -qO $HOME/.sui/genesis.blob https://github.com/MystenLabs/sui-genesis/raw/main/devnet/genesis.blobh
```

```bash
# Copy and edit config (One command)
cp $HOME/sui/crates/sui-config/data/fullnode-template.yaml \
$HOME/.sui/fullnode.yaml && \
sed -i -e "s%db-path:.*%db-path: \"$HOME/.sui/db\"%; "\
"s%metrics-address:.*%metrics-address: \"0.0.0.0:9184\"%; "\
"s%json-rpc-address:.*%json-rpc-address: \"0.0.0.0:9000\"%; "\
"s%genesis-file-location:.*%genesis-file-location: \"$HOME/.sui/genesis.blob\"%; " $HOME/.sui/fullnode.yaml

```

```bash
# Open the ports
. <(wget -qO- https://raw.githubusercontent.com/SecorD0/utils/main/miscellaneous/ports_opening.sh) \
9000 9184
```

```bash
# Create service file (One command)
printf "[Unit]
Description=Sui node
After=network-online.target

[Service]
User=$USER
ExecStart=`which sui-node` --config-path $HOME/.sui/fullnode.yaml
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > /etc/systemd/system/suid.service
```

```bash
# Start the node
sudo systemctl daemon-reload
sudo systemctl enable suid
sudo systemctl restart suid
sudo journalctl -fn 100 -u suid
```
