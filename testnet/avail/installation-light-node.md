# Installation (Light node)

## _**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/avail-light)
```

## _**Manual Installation**_

```bash
# Update, upgrade and install requirements
sudo apt-get update && \
sudo apt-get upgrade -y
# Update or install rust
if command -v rustup &> /dev/null; then
    rustup update
else
    curl https://sh.rustup.rs -sSf | sh
    source $HOME/.cargo/env
fi
rustc --version
sudo apt-get install build-essential cmake clang pkg-config libssl-dev protobuf-compiler git-lfs g++ -y && \
cargo install sccache
```

```bash
cd $HOME
git clone https://github.com/availproject/avail-light.git
cd avail-light
cargo build --release
cp target/release/avail-light /usr/local/bin/avail_light
avail_light --version
# avail-light 1.7.4
```

```bash
sudo bash -c "cat > $HOME/avail-light/config.yaml" <<EOF
log_level = "info"
http_server_host = "127.0.0.1"
http_server_port = 7000

libp2p_port = "37000"

full_node_rpc = ["http://127.0.0.1:9945"]
full_node_ws = ["ws://127.0.0.1:9945"]
app_id = 0
confidence = 92.0
avail_path = "$HOME/.avail-light"
prometheus_port = 9520
# Set to actual bootstrap peer ID and multiaddress
bootstraps = [["12D3KooWStAKPADXqJ7cngPYXd2mSANpdgh1xQ34aouufHA2xShz", "/ip4/127.0.0.1/tcp/39000"]]

EOF
```

```bash
sudo bash -c "cat > /etc/systemd/system/avail_light.service" <<EOF
[Unit] 
Description=Avail Light Client
After=network.target
StartLimitIntervalSec=0
[Service] 
User=$USER 
ExecStart=$(which avail_light) --network goldberg --config $HOME/avail-light/config.yaml
Restart=always 
RestartSec=120
[Install] 
WantedBy=multi-user.target

EOF
```

```bash
systemctl daemon-reload
systemctl enable avail_light
systemctl restart avail_light && journalctl -u avail_light -f -o cat
```
