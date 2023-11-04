# Useful commands

Node

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

Farmer

```bash
# Restart the node and check logs
sudo systemctl restart subspacefarm && sudo journalctl -fu subspacefarm --no-hostname -o cat
```

```bash
# Stop the node
sudo systemctl stop subspacefarm 
```

```bash
# Check the logs
sudo journalctl -fu subspacefarm --no-hostname -o cat
```
