# Update

```bash
cd ~/penumbra && pcli validator definition fetch --file validator.toml
sudo systemctl stop penumbra tendermint
cd ~/penumbra && git fetch && git checkout v0.60.0
cargo build --release --bin pcli
cp ~/penumbra/target/release/pcli /usr/local/bin
cargo build --release --bin pd
cp ~/penumbra/target/release/pd /usr/local/bin
cargo run --quiet --release --bin pcli view reset
cargo run --bin pd --release -- testnet unsafe-reset-all
cargo run --bin pd --release -- testnet join
mv ~/penumbra/validator.toml ~/penumbra/validator59.toml
sudo systemctl restart penumbra tendermint
```

```bash
# Check logs Penumbra
journalctl -u penumbra -f -o cat
```

```bash
# Check logs Tendermint
journalctl -u tendermint -f -o cat
```

```bash
# Check actual block heigth
pcli view sync
```

After synchronization, re-create the validator
