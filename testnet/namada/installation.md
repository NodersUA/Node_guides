# Installation

_**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/nibiru/namada)
```

_**Manual Installation**_

```bash
# Set the variables
echo "export NAMADA_CHAIN_ID=public-testnet-15.0dacadb8d663" >> $HOME/.bash_profile
echo "export NAMADA_PORT=40" >> $HOME/.bash_profile
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
# Update the repositories
sudo apt-get install -y make git-core libssl-dev pkg-config libclang-12-dev build-essential protobuf-compiler
rm /usr/bin/protoc
```

```bash
git clone https://github.com/anoma/namada.git
cd namada 
make install
cp ~/namada/target/release/namada* /usr/local/bin/
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
