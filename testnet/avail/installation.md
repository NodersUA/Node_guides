# Installation

## _**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/avail)
```

## _**Manual Installation**_

```bash
# Update the repositories
sudo apt update
sudo apt install make clang pkg-config libssl-dev build-essential -y
curl https://sh.rustup.rs -sSf | sh
source $HOME/.cargo/env
rustup update nightly
rustup target add wasm32-unknown-unknown --toolchain nightly
rustc --version # Verify Rust installation by displaying the version
```

```bash
cd $HOME
git clone https://github.com/availproject/avail
cd avail/
cargo build --release -p data-avail
cp target/release/data-avail /usr/local/bin/avail
avail --version
# avail 1.8.0-316437486e1
```

```bash
# Open ports
ufw allow 44333
ufw allow 9944
```

```bash
# Enter your moniker
MONIKER=<your_moniker>
```

```bash
sudo bash -c "cat > /etc/systemd/system/availd.service" <<EOF
[Unit]
Description=Avail Validator
After=network.target
StartLimitIntervalSec=0

[Service]
User=$USER
Type=simple
Restart=always
RestartSec=120
ExecStart=$(which avail) \
  --base-path $HOME/.avail/data/ \
  --node-key-file "$HOME/.avail/node-key" \
  --port 44333 \
  --rpc-port 9944 \
  --chain goldberg \
  --validator \
  --name $MONIKER \
  --telemetry-url 'ws://telemetry.avail.tools:8001/submit/ 0'

[Install]
WantedBy=multi-user.target
EOF
```

```bash
systemctl daemon-reload
systemctl enable availd
systemctl restart availd && journalctl -u availd -f -o cat
```

Check your node in [telemetry](https://telemetry.avail.tools/#list/0x6f09966420b2608d1947ccfb0f2a362450d1fc7fd902c29b67c906eaa965a7ae)

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

## Validator

```
Soon...
```
