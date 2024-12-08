# Install

### Automatic Installation

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/midnight)
```

### Cardano Node

Update the repositories

```bash
sudo apt update && sudo apt upgrade -y
```

Install developer packages

```bash
apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev chrony -y
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
git clone git@github.com:midnight-ntwrk/partner-chain-deps-docker.git
cd partner-chain-deps-docker
```

```bash
sed -i 's/3001:3001/3101:3101/' compose.yml
```

```bash
docker-compose up -d
```

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow Ogmios
sudo ufw allow 1337/tcp

# Allow Kupo
sudo ufw allow 1442/tcp

# Allow Postgres-db-sync
sudo ufw allow 5432/tcp

sudo ufw allow ssh

sudo ufw logging on
```

### Cardano Node













\#=======================================================================



```bash
docker search midnightnetwork
```

```bash
docker run -d --name midnight-proof-server -p 6300:6300 midnightnetwork/proof-server -- 'midnight-proof-server --network testnet'
```

```bash
docker logs -f midnight-proof-server
```
