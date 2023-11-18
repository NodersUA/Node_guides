# Update

```bash
cd ~/penumbra && pcli validator definition fetch --file validator.toml
sudo systemctl stop penumbra cometbft

cd ~/penumbra 
git reset --hard HEAD
git fetch && git checkout v0.63.2
rustup update
cargo build --release --bin pcli
cp ~/penumbra/target/release/pcli /usr/local/bin
cargo build --release --bin pd
cp ~/penumbra/target/release/pd /usr/local/bin
cargo run --quiet --release --bin pcli view reset
cargo run --bin pd --release -- testnet unsafe-reset-all
cargo run --bin pd --release -- testnet join
```

```bash
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
