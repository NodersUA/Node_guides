# Useful Commands

```bash
# Check balance
pcli view balance
```

```bash
# Check your address
pcli view address 0
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

```bash
# Restart
sudo systemctl restart penumbra cometbft
```

```bash
# Verify that it’s known to the chain
pcli query validator list -i
```

```bash
# Check your validator
pcli query validator list -i | grep $PENUMBRA_VALIDATOR
```

```bash
# Delegate
pcli tx delegate 1penumbra --to $PENUMBRA_VALIDATOR
```
