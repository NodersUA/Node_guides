# Useful commands

```bash
# Check logs (Linux)
sudo journalctl -fn 100 -u suid
```

```bash
# Restart (Linux)
sudo systemctl restart suid
```

```bash
# Delete DB (Linux)
sudo systemctl stop suid
sudo rm -rf $HOME/.sui/db
sudo systemctl restart suid
```
