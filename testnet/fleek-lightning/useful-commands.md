# Useful Commands

```bash
# Check logs
tail -fn100 /var/log/lightning/output.log
tail -fn100 /var/log/lightning/diagnostic.log
```

```bash
# Restart
sudo systemctl restart lightning
```

```bash
# Check node details
curl https://get.fleek.network/node_details | bash
```

```bash
# Check node health
curl -sS https://get.fleek.network/healthcheck | bash
```

```bash
# Quick health check
curl -w "\n" localhost:4230/health
```
