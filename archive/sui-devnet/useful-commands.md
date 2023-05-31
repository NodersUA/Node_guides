# Useful commands

```bash
# Check logs (Linux)
sudo journalctl -fn 100 -u suid
```

```bash
# Check logs (Docker)
docker logs sui_node -fn100
```

```bash
# Restart (Linux)
systemctl restart suid
```

```bash
# Restart (Docker)
docker restart sui_node
```

```bash
# Delete DB (Linux)
sudo systemctl stop suid
sudo rm -rf $HOME/.sui/db/*
sudo systemctl restart suid
```

```bash
# Delete DB (Docker)
docker stop sui_node
sudo rm -rf $HOME/.sui/db/*
docker start sui_node
```
