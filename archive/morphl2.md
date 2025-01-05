# Morphl2

```bash
sudo apt update && sudo apt install curl git jq lz4 build-essential unzip make gcc cmake clang pkg-config libssl-dev python3-pip protobuf-compiler bc -y
```

```bash
# Install Go
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/system/go)
```

```bash
mkdir -p ~/.morph 
cd ~/.morph
git clone https://github.com/morph-l2/morph.git
```

```bash
cd morph
git checkout v0.2.0-beta
make nccc_geth
```

```bash
cd ~/.morph/morph/node 
make build
```

```bash
cd ~/.morph
wget https://raw.githubusercontent.com/morph-l2/config-template/main/holesky/data.zip
unzip data.zip
openssl rand -hex 32 > jwt-secret.txt
```

```bash
wget -q --show-progress https://snapshot.morphl2.io/holesky/snapshot-20240805-1.tar.gz
tar -xzvf snapshot-20240805-1.tar.gz
```

```bash
rm -rf geth-data/geth/
mv snapshot-20240805-1/geth geth-data
mv snapshot-20240805-1/data node-data
```

```bash
sudo tee /etc/systemd/system/geth.service > /dev/null <<EOF
[Unit]
Description=Geth daemon
After=network-online.target

[Service]
User=root
ExecStart=/root/.morph/morph/go-ethereum/build/bin/geth \
--datadir=/root/.morph/geth-data \
--verbosity=3 \
--http \
--http.corsdomain="*" \
--http.vhosts="*" \
--http.addr=0.0.0.0 \
--http.port=8645 \
--http.api=web3,eth,txpool,net,engine \
--ws \
--ws.addr=0.0.0.0 \
--ws.port=8646 \
--ws.origins="*" \
--ws.api=web3,eth,txpool,net,engine \
--networkid=2810 \
--authrpc.addr="0.0.0.0" \
--authrpc.port="8651" \
--authrpc.vhosts="*" \
--authrpc.jwtsecret=/root/.morph/jwt-secret.txt \
--gcmode=archive \
--metrics \
--metrics.addr=0.0.0.0 \
--metrics.port=6060 \
--miner.gasprice="100000000"
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target

EOF
```

```bash
# Start the node
systemctl daemon-reload
systemctl enable geth
systemctl restart geth && journalctl -u geth -f -o cat
```

```bash
curl --location --request POST 'localhost:8645/' \
--header 'Content-Type: application/json' \
--data-raw '{
   "jsonrpc":"2.0",
   "method":"eth_blockNumber",
   "id":1
}'
```















```
sudo tee /etc/systemd/system/geth.service > /dev/null <<EOF
[Unit]
Description=Geth daemon
After=network-online.target

[Service]
User=root
ExecStart=/root/.morph/morph/go-ethereum/build/bin/geth --morph-holesky \
    --datadir "/root/.morph/geth-data" \
    --http --http.api=web3,debug,eth,txpool,net,engine \
    --authrpc.addr localhost \
    --authrpc.vhosts="localhost" \
    --authrpc.port 8551 \
    --authrpc.jwtsecret=/root/.morph/jwt-secret.txt \
    --log.filename=/root/.morph/geth.log
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target

EOF
```
