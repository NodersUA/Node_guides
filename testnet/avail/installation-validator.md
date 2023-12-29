# Installation (Validator)

## _**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/avail)
```

## _**Manual Installation**_

```bash
# Update the repositories
sudo apt update
sudo apt install make clang pkg-config libssl-dev build-essential protobuf-compiler -y
if [ "$(rustc --version)" != "rustc 1.74.0 (79e9716c9 2023-11-13)" ]; then
curl https://sh.rustup.rs -sSf | sh
source $HOME/.cargo/env
rustup update nightly
rustup target add wasm32-unknown-unknown --toolchain nightly ;
fi
rustc --version # Verify Rust installation by displaying the version
```

```bash
cd $HOME
git clone https://github.com/availproject/avail
cd avail/
cargo build --release -p data-avail
cp target/release/data-avail /usr/local/bin/avail
avail --version
```

```bash
# Open ports
ufw allow 45333
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
  --port 45333 \
  --rpc-port 9945 \
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

* Create [polkadot.js](https://goldberg.avail.tools/#/accounts) account
* Join to [discord](https://discord.gg/6Uy9jK8r)&#x20;
* Go to [#goldberg-faucet](https://discord.com/channels/1065831819154563132/1171414018028740698) channel and send message with your address (5Cyi4FQ........) for request tokens

```bash
/deposit <address>
```

*   Go to **Network - Staking - Accounts** and add stash\


    <figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

*   Connect your stash account with your node\


    <figure><img src="../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

```bash
# Check your session-key
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "author_rotateKeys", "params":[]}' http://localhost:9945
```

Paste key from terminal to browser

<figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

Submit google [form](https://docs.google.com/forms/d/e/1FAIpQLScvgXjSUmwPpUxf1s-MR2C2o5V79TSoud1dLPKVgeLiLFuyGQ/viewform) and wait for the letter
