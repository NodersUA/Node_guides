# Installation

_**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/penumbra)
```

_**Manual Installation**_

1. Install node

```bash
# Update, upgrade and install requirements
sudo apt-get update && \
sudo apt-get upgrade -y && \
sudo curl https://sh.rustup.rs -sSf | sh -s -- -y && \
source "$HOME/.cargo/env" && \
sudo apt-get install build-essential cmake clang pkg-config libssl-dev protobuf-compiler git-lfs g++ -y && \
cargo install sccache
```

```bash
# Clone repository and build
git clone https://github.com/penumbra-zone/penumbra
cd penumbra && git fetch && git checkout v0.64.1
cargo build --release --bin pcli
cp ~/penumbra/target/release/pcli /usr/local/bin
```

2. Create wallet

```bash
pcli init soft-kms generate
```

Save your private seed phrase and beckup file `/root/.local/share/pcli/config.toml` !!!

Or recovery wallet

```bash
pcli init soft-kms import-phrase
```

```bash
# Check your address
pcli view address 0
```

Join the [Discord](https://discord.gg/hKvkrqa3zC) server and post your address in the `#testnet-faucet` channel.

Once you’ve received your first tokens, you can scan the chain to import them into your local wallet (this may take a few minutes the first time you run it):

```bash
# Check balance
pcli view balance
```

3. Using pd

```bash
cargo build --release --bin pd
cp ~/penumbra/target/release/pd /usr/local/bin
```

4. Install Tendermint

```bash
#INSTALL GO
if [ "$(go version)" != "go version go1.20.5 linux/amd64" ]; then \
ver="1.20.5" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile ; \
fi
go version
```

```bash
echo export GOPATH=\"\$HOME/go\" >> ~/.bash_profile
echo export PATH=\"\$PATH:\$GOPATH/bin\" >> ~/.bash_profile
source ~/.bash_profile
```

```bash
if command -v rustup &> /dev/null; then
    cometbft version
else
    cd $HOME
    git clone https://github.com/cometbft/cometbft.git
    cd cometbft
    git checkout v0.37.2
    make install
    cp $(which cometbft) /usr/local/bin/ && cd $HOME
    cometbft version
fi
```

5. Joining as a fullnode

```bash
# Resetting state
pd testnet unsafe-reset-all
```

```bash
# Enter your moniker
MONIKER=<your_moniker>
```

```bash
# Generating configs
pd testnet join --external-address $(wget -qO- eth0.me):26656 --moniker $MONIKER
```

```bash
# Create service file for Penumbra
sudo tee /etc/systemd/system/penumbra.service > /dev/null <<EOF
[Unit]
Description=Penumbra pd
Wants=tendermint.service

[Service]
ExecStart=/usr/local/bin/pd start \
--home $HOME/.penumbra/testnet_data/node0/pd \
--abci-bind 127.0.0.1:42658 \
--cometbft-addr http://127.0.0.1:42657
Restart=on-failure
RestartSec=5
User=$USER
Environment=RUST_LOG=info
LimitNOFILE=65535

[Install]
WantedBy=default.target
EOF
```

```bash
# Create service file for CometBFT
sudo tee /etc/systemd/system/cometbft.service > /dev/null <<EOF
[Unit]
Description=CometBFT for Penumbra

[Service]
ExecStart=/usr/local/bin/cometbft start \
--home $HOME/.penumbra/testnet_data/node0/cometbft \
--proxy_app tcp://127.0.0.1:42658
--rpc.laddr tcp://127.0.0.1:42657
Restart=on-failure
RestartSec=5
User=root
LimitNOFILE=65535

[Install]
WantedBy=default.target
EOF
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable penumbra cometbft
sudo systemctl restart penumbra cometbft
```

```bash
# Check logs Penumbra
journalctl -u penumbra -f -o cat
```

```bash
# Check logs CometBFT
journalctl -u cometbft -f -o cat
```

```bash
# Check actual block heigth
pcli view sync
```

6. Validator

```bash
# Create validator
cd ~/penumbra && pcli validator definition template \
    --tendermint-validator-keyfile ~/.penumbra/testnet_data/node0/cometbft/config/priv_validator_key.json \
    --file validator.toml
    
sed -i "s/^name *=.*/name = \"$MONIKER\"/" ~/penumbra/validator.toml
sed -i "s/^enabled *=.*/enabled = true/" ~/penumbra/validator.toml
sed -i "s/^sequence_number *=.*/sequence_number = 1/" ~/penumbra/validator.toml
```

```bash
# Save the wallet and validator address
PENUMBRA_VALIDATOR=$(grep -E '^identity_key = "(.*)"' validator.toml | awk -F '"' '{print $2}')
PENUMBRA_ADDRESS=$(grep -E 'recipient = "(.*)"' validator.toml | awk -F '"' '{print $2}')
echo "export PENUMBRA_ADDRESS="${PENUMBRA_ADDRESS} >> $HOME/.bash_profile
echo "export PENUMBRA_VALIDATOR="${PENUMBRA_VALIDATOR} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
# upload validator to the chain
pcli validator definition upload --file ~/penumbra/validator.toml
```

```bash
# Verify that it’s known to the chain
pcli query validator list -i
```

```bash
# Check your validator
pcli query validator list -i | grep $PENUMBRA_VALIDATOR
```

Delegating to your validator

```bash
pcli tx delegate 100penumbra --to $PENUMBRA_VALIDATOR
```

```bash
# Check balance
pcli view balance
```

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>
