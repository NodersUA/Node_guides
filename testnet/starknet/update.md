# Update

```bash
# Get last version
sudo docker pull eqlabs/pathfinder

# This stops the running instance
sudo docker stop pathfinder
# This removes the current instance (using the old version of pathfinder)
sudo docker rm pathfinder

# Delete DB
sudo rm -rf ~/pathfinder/*

# Install RClone
sudo -v ; curl https://rclone.org/install.sh | sudo bash
mkdir -p ~/.config/rclone/
sudo tee ~/.config/rclone/rclone.conf > /dev/null <<EOF
[pathfinder-snapshots]
type = s3
provider = Cloudflare
env_auth = false
access_key_id = 7635ce5752c94f802d97a28186e0c96d
secret_access_key = 529f8db483aae4df4e2a781b9db0c8a3a7c75c82ff70787ba2620310791c7821
endpoint = https://cbf011119e7864a873158d83f3304e27.r2.cloudflarestorage.com
acl = private
EOF

rclone copy -P pathfinder-snapshots:pathfinder-snapshots/mainnet_0.9.0_309113.sqlite.zst .
sudo apt install zstd
zstd -T0 -d mainnet_0.9.0_309113.sqlite.zst -o ~/pathfinder/mainnet.sqlite


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
