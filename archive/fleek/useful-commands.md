# Useful Commands

```bash
# Check logs
sudo journalctl -u fleekd -f -o cat
```

```bash
# Restart
sudo systemctl restart fleekd && sudo journalctl -u fleekd -f -o cat
```
