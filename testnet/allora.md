# Allora

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install ca-certificates zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev curl git wget make -y
```

#### Install Python <a href="#install-docker" id="install-docker"></a>

```bash
if ! [ -x "$(command -v python3)" ]; then
sudo apt install python3
python3 --version
else
python3 --version
fi
```

```bash
if ! dpkg -s python3-pip &> /dev/null; then
sudo apt install python3-pip
pip3 --version
else
pip3 --version
fi
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
# Install Go
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/system/go)
```

#### Install node

```bash
git clone https://github.com/allora-network/allora-chain.git
cd allora-chain && make all
allorad version
```

```bash
allorad keys add wallet --keyring-backend test
```

* Go to [explorer](https://explorer.edgenet.allora.network/wallet/suggest) and add the Allora network using Keplr

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*pw0_gipceAqATXebqIUsWw.png" alt=""><figcaption></figcaption></figure>

* Go to the [faucet](https://faucet.edgenet.allora.network/) and request $uAllo test tokens

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*uNwRL94FPGxhxBz6EPGYmg.png" alt=""><figcaption></figcaption></figure>

<figure><img src="https://miro.medium.com/v2/resize:fit:574/1*EaVBVYUS6XrvvT0bHdhohQ.png" alt=""><figcaption></figcaption></figure>

```
cd $HOME && git clone https://github.com/allora-network/basic-coin-prediction-node
cd basic-coin-prediction-node
mkdir worker-data
mkdir head-data
```

```
sudo chmod -R 777 worker-data
sudo chmod -R 777 head-data
```

* Create a key

```
sudo docker run -it --entrypoint=bash -v ./head-data:/data alloranetwork/allora-inference-base:latest -c "mkdir -p /data/keys && (cd /data/keys && allora-keys)"
```

* Create a worker key

```
sudo docker run -it --entrypoint=bash -v ./worker-data:/data alloranetwork/allora-inference-base:latest -c "mkdir -p /data/keys && (cd /data/keys && allora-keys)"
```

```bash
HEAD_ID=$(cat ~/basic-coin-prediction-node/head-data/keys/identity)
```

```bash
MNEMONIC=""
```

```bash
sudo tee ~/basic-coin-prediction-node/docker-compose.yml > /dev/null <<EOF
version: '3'

services:
  inference:
    container_name: inference-basic-eth-pred
    build:
      context: .
    command: python -u /app/app.py
    ports:
      - "8000:8000"
    networks:
      eth-model-local:
        aliases:
          - inference
        ipv4_address: 172.23.0.4
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/inference/ETH"]
      interval: 10s
      timeout: 5s
      retries: 12
    volumes:
      - ./inference-data:/app/data

  updater:
    container_name: updater-basic-eth-pred
    build: .
    environment:
      - INFERENCE_API_ADDRESS=http://inference:8000
    command: >
      sh -c "
      while true; do
        python -u /app/update_app.py;
        sleep 24h;
      done
      "
    depends_on:
      inference:
        condition: service_healthy
    networks:
      eth-model-local:
        aliases:
          - updater
        ipv4_address: 172.23.0.5

  worker:
    container_name: worker-basic-eth-pred
    environment:
      - INFERENCE_API_ADDRESS=http://inference:8000
      - HOME=/data
    build:
      context: .
      dockerfile: Dockerfile_b7s
    entrypoint:
      - "/bin/bash"
      - "-c"
      - |
        if [ ! -f /data/keys/priv.bin ]; then
          echo "Generating new private keys..."
          mkdir -p /data/keys
          cd /data/keys
          allora-keys
        fi
        # Change boot-nodes below to the key advertised by your head
        allora-node --role=worker --peer-db=/data/peerdb --function-db=/data/function-db \
          --runtime-path=/app/runtime --runtime-cli=bls-runtime --workspace=/data/workspace \
          --private-key=/data/keys/priv.bin --log-level=debug --port=9011 \
          --boot-nodes=/ip4/172.23.0.100/tcp/9010/p2p/$HEAD_ID \
          --topic=allora-topic-1-worker \
          --allora-chain-key-name=wallet \
          --allora-chain-restore-mnemonic='$MNEMONIC' \
          --allora-node-rpc-address=https://allora-rpc.edgenet.allora.network/ \
          --allora-chain-topic-id=1
    volumes:
      - ./worker-data:/data
    working_dir: /data
    depends_on:
      - inference
      - head
    networks:
      eth-model-local:
        aliases:
          - worker
        ipv4_address: 172.23.0.10

  head:
    container_name: head-basic-eth-pred
    image: alloranetwork/allora-inference-base-head:latest
    environment:
      - HOME=/data
    entrypoint:
      - "/bin/bash"
      - "-c"
      - |
        if [ ! -f /data/keys/priv.bin ]; then
          echo "Generating new private keys..."
          mkdir -p /data/keys
          cd /data/keys
          allora-keys
        fi
        allora-node --role=head --peer-db=/data/peerdb --function-db=/data/function-db  \
          --runtime-path=/app/runtime --runtime-cli=bls-runtime --workspace=/data/workspace \
          --private-key=/data/keys/priv.bin --log-level=debug --port=9010 --rest-api=:6000
    ports:
      - "6000:6000"
    volumes:
      - ./head-data:/data
    working_dir: /data
    networks:
      eth-model-local:
        aliases:
          - head
        ipv4_address: 172.23.0.100


networks:
  eth-model-local:
    driver: bridge
    ipam:
      config:
        - subnet: 172.23.0.0/24

volumes:
  inference-data:
  worker-data:
  head-data:
EOF
```

```bash
docker system prune -a --volumes
docker compose build && docker compose up -d
```
