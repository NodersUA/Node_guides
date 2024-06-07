# Installation

## _**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/namada)
```

## _**Manual Installation**_

```bash
# Set the variables
echo "export NAMADA_CHAIN_ID=shielded-expedition.b40d8e9055" >> $HOME/.bash_profile
echo "export NAMADA_PORT=41" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
# Update the repositories
apt update && apt upgrade -y
```

```bash
# Update or install rust
if command -v rustup &> /dev/null; then
    rustup update
else
    curl https://sh.rustup.rs -sSf | sh
    source $HOME/.cargo/env
fi
rustc --version # Verify Rust installation by displaying the version
```

```bash
if command -v cometbft &> /dev/null; then
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

```bash
# Update the repositories
sudo apt-get install -y make git-core libssl-dev pkg-config libclang-12-dev build-essential protobuf-compiler
```

```bash
rm /usr/bin/protoc
wget https://github.com/protocolbuffers/protobuf/releases/download/v3.12.0/protobuf-all-3.12.0.tar.gz
tar -xzvf protobuf-all-3.12.0.tar.gz
cd protobuf-3.12.0
./configure
make
sudo make install
export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
```

```bash
cd $HOME && git clone https://github.com/anoma/namada.git
cd namada && git checkout v0.31.0
make install
```

```
cp ~/namada/target/release/namada* /usr/local/bin/
namada --version
```

```bash
namada client utils join-network --chain-id $NAMADA_CHAIN_ID
```

```bash
sed -i.bak -e "s%:26658%:${NAMADA_PORT}658%g;
s%:26657%:${NAMADA_PORT}657%g;
s%:26656%:${NAMADA_PORT}656%g;
s%:26545%:${NAMADA_PORT}545%g;
s%:8545%:${NAMADA_PORT}545%g;
s%:26660%:${NAMADA_PORT}660%g" ~/.local/share/namada/$NAMADA_CHAIN_ID/config.toml
```

```bash
# Open ports
ufw allow ${NAMADA_PORT}658
ufw allow ${NAMADA_PORT}657
ufw allow ${NAMADA_PORT}545
ufw allow ${NAMADA_PORT}660
```

```bash
sudo tee /etc/systemd/system/namadad.service > /dev/null <<EOF
[Unit]
Description=namada
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.local/share/namada
Environment=CMT_LOG_LEVEL=p2p:none,pex:error
Environment=NAMADA_CMT_STDOUT=true
ExecStart=/usr/local/bin/namada node ledger run 
StandardOutput=syslog
StandardError=syslog
Restart=always
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable namadad
sudo systemctl restart namadad
```

```bash
sudo journalctl -u namadad -f -o cat
```

### Wallet

```bash
# Create wallet
namada wallet address gen --alias wallet
```

```bash
# Or Recovery wallet
namada wallet key derive --alias wallet --hd-path default
```

```bash
# Check wallet list
namada wallet key list
```

```bash
# Check wallet address
namada wallet address list
```
