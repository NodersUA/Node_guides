# Installation

_**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/starknet)
```

_**Manual Installation**_

```bash
# Update the repositories
apt update && apt upgrade -y
```

```bash
# Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo usermod -aG docker $USER
docker --version
```

```bash
# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.2.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose 
sudo chmod +x /usr/local/bin/docker-compose 
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
docker-compose --version
```

```bash
# Set the variables
STARKNET_RPC=<your_ethereum_mainnet_rpc>
echo 'export STARKNET_RPC='$STARKNET_RPC >> $HOME/.bash_profile
source $HOME/.bash_profile
```

Get ethereum\_mainnet\_rpc on one of [Alchemy](https://www.alchemy.com/) and [Infura](https://www.infura.io/) services

```bash
# Create a directory
mkdir -p $HOME/pathfinder
```

```bash
# Start the pathfinder container
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
```

```bash
# Check logs
sudo docker logs -f pathfinder
```
