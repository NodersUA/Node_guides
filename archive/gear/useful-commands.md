# Useful Commands

```bash
# Restart and the node
sudo systemctl restart gear-node && sudo journalctl -fu gear-node --no-hostname -o cat
```

```bash
# Check the logs
sudo journalctl -fu gear-node --no-hostname -o cat
```

```bash
# If the height of the block does not increase
rm /tmp/substrate-wasmer-cache/
sudo systemctl restart gear-node && sudo journalctl -fu gear-node --no-hostname -o cat
```
