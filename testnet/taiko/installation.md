# Installation

## Receive tokens <a href="#top" id="top"></a>

### Receive some Holesky testnet Ether

Taiko (Katla) is currently deployed on Holesky testnet. Check out [faucetlink.to](https://faucetlink.to/) for a list of Holesky ether faucets!

### Receive some Katla testnet tokens <a href="#receive-some-katla-testnet-tokens" id="receive-some-katla-testnet-tokens"></a>

HORSE is a dummy testnet token we deployed on Taiko (Katla). You can see all deployed contracts [here](https://docs.taiko.xyz/network-reference/addresses). Navigate to the [bridge](https://bridge.katla.taiko.xyz/) and click “Faucet” on the sidebar to receive some HORSE.

## Install Node

_**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/taiko)
```

_**Manual Installation**_

```bash
# Update the repositories
apt update && apt upgrade -y
sudo apt-get install git-all -y
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
git clone https://github.com/taikoxyz/simple-taiko-node.git taiko
cd taiko
cp .env.sample .env
```

```bash
# Set the variables
RPC_HOLESKY=<your_holesky_rpc>
WS_HOLESKY=<your_holesky_ws>
L1_PROPOSER_PRIVATE_KEY=<your_private_key>
EVM_ADDRESS=<your_evm_address>

echo 'export RPC_HOLESKY='$RPC_HOLESKY >> $HOME/.bash_profile
echo 'export WS_HOLESKY='$WS_HOLESKY >> $HOME/.bash_profile
echo 'export EVM_ADDRESS='$EVM_ADDRESS >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
sed -i "s|^L1_ENDPOINT_HTTP=.*|L1_ENDPOINT_HTTP=$RPC_HOLESKY|" .env
sed -i "s|^L1_ENDPOINT_WS=.*|L1_ENDPOINT_WS=$WS_HOLESKY|" .env
sed -i 's/^PORT_GRAFANA=.*/PORT_GRAFANA=4301/' .env
sed -i 's/^PORT_PROMETHEUS=.*/PORT_PROMETHEUS=9043/' .env
sed -i 's/^ENABLE_PROPOSER=.*/ENABLE_PROPOSER=true/' .env
sed -i 's/^PROVER_ENDPOINTS=.*/PROVER_ENDPOINTS=http://taiko-a6-prover.zkpool.io:9876/' .env
sed -i "s/^L1_PROPOSER_PRIVATE_KEY=.*/L1_PROPOSER_PRIVATE_KEY=$L1_PROPOSER_PRIVATE_KEY/" .env
sed -i "s/^L2_SUGGESTED_FEE_RECIPIENT=.*/L2_SUGGESTED_FEE_RECIPIENT=$EVM_ADDRESS/" .env
```

```bash
docker compose --profile l2_execution_engine up -d
docker compose --profile prover up -d
```

Check your node in dashbord - [http://\<your\_ip>:4301/d/L2ExecutionEngine/l2-execution-engine-overview?orgId=1\&refresh=10s](http://46.4.101.90:4301/d/L2ExecutionEngine/l2-execution-engine-overview?orgId=1\&refresh=10s)

```bash
# Check logs
cd ~/taiko && docker-compose logs -f --tail 100
```

```bash
# Restart
cd ~/taiko && docker compose down && docker compose up -d
```
