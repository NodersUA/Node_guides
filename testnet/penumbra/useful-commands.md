# Useful Commands

```bash
# Check balance
pcli view balance
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

```bash
# Restart
sudo systemctl restart penumbra tendermint
```

```bash
# Verify that it’s known to the chain
pcli query validator list -i
```

```bash
# Delegate
pcli tx delegate 1penumbra --to $PENUMBRA_VALIDATOR
```
