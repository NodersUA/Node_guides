# Update

```bash
# Get last version
sudo docker pull eqlabs/pathfinder

# This stops the running instance
sudo docker stop pathfinder
# This removes the current instance (using the old version of pathfinder)
sudo docker rm pathfinder
# This command re-creates the container instance with the latest version
sudo docker run \
  --name pathfinder \
  --restart unless-stopped \
  --detach \
  -p 9545:9545 \
  --user "$(id -u):$(id -g)" \
  -e RUST_LOG=info \
  -e PATHFINDER_ETHEREUM_API_URL=$STARKNET_RPC \
  -v $HOME/pathfinder:/usr/share/pathfinder/data \
  eqlabs/pathfinder
  
# Check logs
sudo docker logs pathfinder -fn100
```
