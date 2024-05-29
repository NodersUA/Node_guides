# Update

```bash
sudo systemctl stop penumbra cometbft
```

```bash
if [ "$(cometbft version)" != "0.37.5" ]; then
    rm -rf ~/cometbft/
    rm /usr/local/bin/cometbft
    cd $HOME
    git clone https://github.com/cometbft/cometbft.git
    cd cometbft
    git checkout v0.37.5
    make install
    cp $(which cometbft) /usr/local/bin/ && cd $HOME
    cometbft version
fi
```

```bash
cd ~/penumbra 
git reset --hard HEAD
git fetch && git checkout v0.73.0
cargo build --release --bin pcli
cp ~/penumbra/target/release/pcli /usr/local/bin
cargo build --release --bin pd
cp ~/penumbra/target/release/pd /usr/local/bin
pcli view reset
cargo run --bin pd --release -- testnet unsafe-reset-all
```

```bash
pd testnet join --external-address $(curl -s https://checkip.amazonaws.com):42656 --moniker $MONIKER --archive-url "https://snapshots.penumbra.zone/testnet/pd-migrated-state-75-76.tar.gz"
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
