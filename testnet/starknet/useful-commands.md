# Useful Commands

```bash
# Check logs
sudo docker logs pathfinder -fn100
```

```bash
# Restart
sudo docker restart pathfinder && sudo docker logs pathfinder -fn100
```

```bash
# Check version
sudo docker exec -it pathfinder pathfinder -V
```
