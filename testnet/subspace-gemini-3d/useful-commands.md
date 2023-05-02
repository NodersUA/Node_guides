# Useful commands

```bash
# Restart and the node
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
subspace wipe && rm -Rvf $HOME/.local/share/subspace-cli/node/chains/*
```

```bash
# Edit config
nano $HOME/.config/subspace-cli/settings.toml
```

```bash
# Delete log files
rm $HOME/.local/share/subspace-cli/logs/*
```
