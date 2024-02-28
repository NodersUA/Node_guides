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
# Update or install rust
if command -v rustup &> /dev/null; then
    rustup update
else
    curl https://sh.rustup.rs -sSf | sh
    source $HOME/.cargo/env
fi
rustc --version
```

```bash
cd $HOME
git clone https://github.com/webb-tools/tangle.git
cd tangle/
git checkout v0.6.1
cargo build --release
cp target/release/tangle /usr/local/bin/
tangle --version
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
  --chain tangle-testnet \
  --node-key-file "$HOME/.tangle/node-key" \
  --port 43333 \
  --rpc-port 9943 \
  --validator \
  --no-mdns \
  --auto-insert-keys \
  --rpc-cors all \
  --pruning archive \
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

Check your node in [telemetry](https://telemetry.polkadot.io/#list/0x3d22af97d919611e03bbcbda96f65988758865423e89b2d99547a6bb61452db3)

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

## Validator

1. Create [polkadot.js](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Ftestnet-rpc.tangle.tools#/accounts) account or connect your subwallet
2.  Set on-chain identity\


    <figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

    <figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
3. Join to [discord](https://discord.com/invite/cv8EfJu3Tn)&#x20;
4.  Go to [#](https://discord.com/channels/833784453251596298/1106624706813112351)[-faucet](https://discord.com/channels/833784453251596298/1183826417625075753) channel and send message with your address (!send 5FejFZ55......) for request tokens\


    <figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

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
