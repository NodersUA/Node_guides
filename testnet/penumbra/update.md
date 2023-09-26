# Update

```bash
cd ~/penumbra && pcli validator definition fetch --file validator.toml
sudo systemctl stop penumbra tendermint
cd ~/penumbra && git fetch && git checkout v0.61.0
cargo build --release --bin pcli
cp ~/penumbra/target/release/pcli /usr/local/bin
cargo build --release --bin pd
cp ~/penumbra/target/release/pd /usr/local/bin
cargo run --quiet --release --bin pcli view reset
cargo run --bin pd --release -- testnet unsafe-reset-all
cargo run --bin pd --release -- testnet join
mv ~/penumbra/validator.toml ~/penumbra/validator60.toml
```

```bash
# Create service file for CometBFT
sudo tee /etc/systemd/system/cometbft.service > /dev/null <<EOF
[Unit]
Description=CometBFT for Penumbra

[Service]
ExecStart=/usr/local/bin/cometbft start --home $HOME/.penumbra/testnet_data/node0/cometbft
Restart=on-failure
RestartSec=5
User=root

[Install]
WantedBy=default.target
EOF
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable cometbft
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

After synchronization, re-create the validator
