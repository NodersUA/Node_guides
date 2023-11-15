# Useful Commands

## Full Node

```bash
# Restart
sudo systemctl restart availd
```

```bash
# Check logs
journalctl -u availd -f -o cat
```

## Light Node

```bash
# Restart
sudo systemctl restart avail_light
```

```bash
# Check logs
journalctl -u avail_light -f -o cat
```

```bash
# Check health
curl -I "localhost:7000/health"
```
