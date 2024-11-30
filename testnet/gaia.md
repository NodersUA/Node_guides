# Gaia

```bash
curl -sSfL 'https://github.com/GaiaNet-AI/gaianet-node/releases/latest/download/install.sh' | bash
```

```bash
source ~/.bashrc
```

```bash
curl -L https://raw.githubusercontent.com/GaiaNet-AI/node-configs/main/llama-3-8b-instruct_london/config.json > ~/gaianet/config.json
```

```bash
sed -i 's/"llamaedge_port": "8080"/"llamaedge_port": "8182"/' ~/gaianet/config.json
```

```bash
gaianet init
```

Docker

```bash
git clone https://github.com/GaiaNet-AI/gaianet-node.git
cd gaianet-node
```

```bash
docker run --name gaianet \
  -p 8182:8182 \
  -v $(pwd)/qdrant_storage:/root/gaianet/qdrant/storage:z \
  gaianet/phi-3-mini-instruct-4k_paris:latest
```

```bash
bash <(curl -s "https://raw.githubusercontent.com/0ndrec/crocogaia/main/install.sh")
```
