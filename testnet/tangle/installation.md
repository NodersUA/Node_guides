# Installation

## _**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/tangle)
```

## _**Manual Installation**_

```bash
# Update the repositories
apt update && apt upgrade -y
apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev libgmp3-dev tar clang bsdmainutils ncdu unzip llvm libudev-dev make protobuf-compiler -y
```

```bash
cd $HOME
git clone https://github.com/webb-tools/tangle.git
cd tangle/
cargo build --release
cp target/release/tangle-standalone /usr/local/bin/tangle
tangle --version
```

```bash
mkdir -p $HOME/.tangle
wget -O $HOME/.tangle/tangle-standalone.json "https://raw.githubusercontent.com/webb-tools/tangle/main/chainspecs/testnet/tangle-standalone.json"
chmod 744 ~/.tangle/tangle-standalone.json
```

```bash
# Open ports
ufw allow 43333
ufw allow 9943
```

```bash
# Enter your moniker
MONIKER=<your_moniker>
```

```bash
sudo tee /etc/systemd/system/tangle.service > /dev/null << EOF
[Unit]
Description=Tangle Validator Node
After=network-online.target
StartLimitIntervalSec=0
 
[Service]
User=$USER
Restart=always
RestartSec=3
ExecStart=/usr/local/bin/tangle \
  --base-path $HOME/.tangle/data/ \
  --name $MONIKER \
  --chain $HOME/.tangle/tangle-standalone.json \
  --node-key-file "$HOME/.tangle/node-key" \
  --port 43333 \
  --rpc-port 9943 \
  --validator \
  --no-mdns \
  --auto-insert-keys \
  --telemetry-url "wss://telemetry.polkadot.io/submit/ 0"
 
[Install]
WantedBy=multi-user.target
EOF
```

```bash
systemctl daemon-reload
systemctl enable tangle
systemctl restart tangle && journalctl -u tangle -f -o cat
```

Check your node in [telemetry](https://telemetry.polkadot.io/#list/0xea63e6ac7da8699520af7fb540470d63e48eccb33f7273d2e21a935685bf1320)

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

## Validator

1. Create [polkadot.js](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Frpc-archive.tangle.tools#/staking) account or connect your subwallet
2.  Set on-chain identity\


    <figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

    <figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
3. Join to [discord](https://discord.com/invite/cv8EfJu3Tn)&#x20;
4. Go to [#validators](https://discord.com/channels/833784453251596298/1106624706813112351) channel and send message with your address (5Cyi4FQ........) for request tokens

*   Go to **Network - Staking - Accounts** and add stash\


    <figure><img src="../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>
*   Connect your stash account with your node\


    <figure><img src="../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

```bash
# Check your session-key
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "author_rotateKeys", "params":[]}' http://localhost:9943
```

Paste key from terminal to browser

*   Active validator\


    <figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>
*   Check your validator\


    <figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>
