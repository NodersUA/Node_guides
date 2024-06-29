# Install

### Setting up a Union node

```bash
# Set the variables

# Come up with the name of your node and replace it instead <your_moniker>
ETH_MAINNET_RPC=<your-ETH-mainnet-RPC-URL>
OPTIMISM_L2_RPC=<your-L2-optimism-RPC-URL>
HUB_OPERATOR_FID=<your-fid>

echo "export ETH_MAINNET_RPC="$ETH_MAINNET_RPC >> $HOME/.bash_profile
echo "export OPTIMISM_L2_RPC="$OPTIMISM_L2_RPC >> $HOME/.bash_profile
echo "export HUB_OPERATOR_FID="$HUB_OPERATOR_FID >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install make clang pkg-config libssl-dev libclang-dev build-essential git curl ntp jq llvm tmux htop screen unzip cmake -y
```

```bash
# Install Go
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/system/go)
```

#### Install docker <a href="#install-docker" id="install-docker"></a>

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
# Install Docker Compose
if ! [ -x "$(command -v docker-compose)" ]; then
sudo curl -L "https://github.com/docker/compose/releases/download/v2.2.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose 
sudo chmod +x /usr/local/bin/docker-compose 
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
docker-compose --version
else
echo $(docker-compose --version)
fi
```

```bash
git clone -c advice.detachedHead=false -b @latest https://github.com/farcasterxyz/hub-monorepo.git
```

```bash
cd hub-monorepo/apps/hubble
docker compose run --user root hubble yarn identity create
docker compose run --user root hubble chmod -R 777 /home/node/app/apps/hubble/.rocks
```

```bash
# Create service file (One command)
sudo tee ~/hub-monorepo/apps/hubble/.env > /dev/null <<EOF
ETH_MAINNET_RPC_URL=$ETH_MAINNET_RPC
OPTIMISM_L2_RPC_URL=$OPTIMISM_L2_RPC
HUB_OPERATOR_FID=$HUB_OPERATOR_FID
FC_NETWORK_ID=1
BOOTSTRAP_NODE=/dns/nemes.farcaster.xyz/tcp/2282
EOF
```

```bash
docker compose up hubble -d
```

```bash
docker compose logs -fn10 hubble
```
