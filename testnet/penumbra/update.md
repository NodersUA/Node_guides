# Update

```bash
sudo systemctl stop penumbra tendermint
cd ~/penumbra && git fetch && git checkout v0.58.0
cargo build --release --bin pcli
cp ~/penumbra/target/release/pcli /usr/local/bin
cargo run --quiet --release --bin pcli view reset
cargo run --bin pd --release -- testnet unsafe-reset-all
cargo run --bin pd --release -- testnet join
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
