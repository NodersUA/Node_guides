# Update

```bash
echo "export NAMADA_CHAIN_ID=shielded-expedition.b40d8e9055" >> $HOME/.bash_profile
source $HOME/.bash_profile
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
cd $HOME && git clone https://github.com/anoma/namada.git
cd namada && git checkout v0.31.0
make install
```

```bash
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
sudo systemctl restart namadad && sudo journalctl -u namadad -f -o cat
```

```bash
# Recovery wallet
namada wallet key derive --alias wallet --hd-path default
```
