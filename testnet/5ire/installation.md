# Installation

### Manual Installation

```bash
# Update the repositories
apt update && apt upgrade -y
```

```bash
# Install Docker
if ! [ -x "$(command -v docker)" ]; then
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo usermod -aG docker $USER
docker --version
else
echo $(docker --version)
fi
```

```bash
# Set the variables
MONIKER=<YOUR_MONIKER>
FIVEIRE_PORT=44
echo 'export MONIKER='$MONIKER >> $HOME/.bash_profile
echo 'export FIVEIRE_PORT='$FIVEIRE_PORT >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
# Open ports
ufw allow 30${FIVEIRE_PORT}3
ufw allow 9${FIVEIRE_PORT}4
ufw allow 9${FIVEIRE_PORT}3
```

```bash
# Start the 5ire container
sudo docker run \
  --name 5ire \
  --restart unless-stopped \
  --detach \
  -p 30${FIVEIRE_PORT}3:30${FIVEIRE_PORT}3 \
  -p 9${FIVEIRE_PORT}3:9${FIVEIRE_PORT}3 \
  -p 9${FIVEIRE_PORT}4:9${FIVEIRE_PORT}4 5irechain/5ire-thunder-node:0.12 \
  --port 30${FIVEIRE_PORT}3 \
  --no-telemetry \
  --name $MONIKER \
  --base-path /5ire/data \
  --keystore-path /5ire/data \
  --node-key-file /5ire/secrets/node.key \
  --chain /5ire/thunder-chain-spec.json \
  --bootnodes /ip4/3.128.99.18/tcp/30333/p2p/12D3KooWSTawLxMkCoRMyzALFegVwp7YsNVJqh8D2p7pVJDqQLhm \
  --validator
```

```bash
# Check logs
sudo docker logs -fn100 5ire
```
