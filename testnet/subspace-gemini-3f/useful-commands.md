# Useful commands

```bash
# Restart the node and check logs
sudo systemctl restart subspaced && sudo journalctl -fu subspaced --no-hostname -o cat
```

```bash
# Stop the node
sudo systemctl stop subspaced
```

```bash
# Check the logs
sudo journalctl -fu subspaced --no-hostname -o cat
```

```bash
# Clean the database
subspace wipe && rm -Rvf $HOME/.local/share/pulsar/node/chains/*
```

```bash
# Edit config
nano $HOME/.config/pulsar/settings.toml
```

```bash
# Delete log files
rm $HOME/.local/share/pulsar/logs/*
```

```bash
# Check version
subspace -V
```
