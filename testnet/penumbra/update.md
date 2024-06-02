# Update

```bash
sudo systemctl stop penumbra cometbft
```

```bash
cd ~/penumbra 
git reset --hard HEAD
git fetch && git checkout v0.77.0
cargo build --release --bin pcli
cp ~/penumbra/target/release/pcli /usr/local/bin
cargo build --release --bin pd
cp ~/penumbra/target/release/pd /usr/local/bin
```

```bash
pd migrate
```

```bash
sudo systemctl restart penumbra cometbft
```

```bash
pcli view reset
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
