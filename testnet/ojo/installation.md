**Automatic Installation**
```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/ojo) 
```

**Manual Installation**
```bash
# Update the repositories
apt update && apt upgrade -y
```
```bash
# Install developer packages
apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```
```bash
# Install Go (one command)
ver="1.20.1" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version

# go version go1.20.1 linux/amd64
```
```bash
# Set the variables

# Come up with the name of your node and replace it instead <your_moniker>
MONIKER_OJO=<your_moniker>

echo 'export MONIKER_OJO='$MONIKER_OJO >> $HOME/.bash_profile
echo "export CHAIN_ID_OJO=ojo-devnet" >> $HOME/.bash_profile
echo "export PORT_OJO=37" >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```
```bash
# Download binary files
cd $HOME 
git clone https://github.com/ojo-network/ojo
cd ojo
git fetch --all 
git checkout v0.1.2
make install
sudo cp $HOME/go/bin/ojod /usr/local/bin/ojod
ojod version --long | grep -e version -e commit -e build
# HEAD-ad5a2377134aa13d7d76575b95613cf8ed12d1e4
# commit: ad5a2377134aa13d7d76575b95613cf8ed12d1e4
# v0.1.2
```
```bash
# Initialize the node
ojod init $MONIKER_OJO --chain-id $CHAIN_ID_OJO
```
```bash
# Download Genesis
curl -Ls https://rpc.devnet-n0.ojo-devnet.node.ojo.network/genesis > $HOME/.ojo/config/genesis.json

# Check Genesis
sha256sum $HOME/.ojo/config/genesis.json
```
```bash
# Set the ports

# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${PORT_OJO}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${PORT_OJO}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${PORT_OJO}061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${PORT_OJO}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${PORT_OJO}660\"%" $HOME/.ojo/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${PORT_OJO}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${PORT_OJO}91\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${PORT_OJO}27\"%" $HOME/.ojo/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${PORT_OJO}657\"%" $HOME/.ojo/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:${PORT_OJO}656\"/" $HOME/.ojo/config/config.toml
```

***Setup config***
```bash
# correct config (so we can no longer use the chain-id flag for every CLI command in client.toml)
ojod config chain-id ojo-devnet

# adjust if necessary keyring-backend в client.toml 
ojod config keyring-backend test

ojod config node tcp://localhost:${PORT_OJO}657

# Set the minimum price for gas
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025uojo\"/;" ~/.ojo/config/app.toml

# Add seeds/peers в config.toml
PEERS="17a5fad48064ee3da42f435925f7bbe055e6348d@ojo-testnet.nodejumper.io:37656,2155de2f62e75c9a5b0c013c756420dd23f59914@142.132.209.236:21656,fb10560d2e3aea7948a375dc87140c156a07acc4@195.201.83.242:17656,0621bb73d18724cae4eb411e6b96765f95a3345e@178.63.8.245:61356,bef511f2c5244e6603bd74295e2dffb126d04f41@158.101.208.86:26656,9ebe723eef929e9eff748f4046d6130ee349a398@65.108.203.149:24017,8fbfa810cb666ddef1c9f4405e933ef49138f35a@65.108.199.120:54656,dd55e293588003da8cd6cf56a0e6c6cdab1f6e75@65.109.88.254:46656,4e309b79b9147a0243f6e0cbc824f86e10bd09de@65.109.234.254:50656,b0dac6c4a34dff86d3a77665c61bd08b4a5007cf@65.108.224.156:26656,5d9be9cf3d5161e4891b96a956c3c83de6c0ae49@5.78.75.124:26656,da369d44c00dba309237b21391806504353d188f@194.163.187.175:50656,f702b19a4dae5ad813dabe3f529bf31c160a74e0@5.189.176.202:36656,59954989ec7cb0c12ec55128d142db1a274b4465@135.181.221.186:26656,1f4686345fbb19f91bfef4545b7b3742ad8a15d6@65.108.71.92:55756,97a388be825fc69fca40a8a3de75aa5794602abb@95.217.225.212:36656,3c6384ae2a167912a5ace2f5f8e38afc559715f0@75.119.156.88:26656,2905d22a658a7138c03c0259fba4c168260682bb@159.69.208.78:26656,124439d1c16b1ee7ca1a39961f02fadf8539cb81@38.102.85.10:26656,d04194300a0e6e1c480a5f264eface1c8b09b95c@159.148.146.144:26656,8f414276a2cb7a97d37a3e126c186972e1968039@65.108.4.233:56656,21f35146e8905d6786b69486b1d27b680fb3c548@65.109.89.5:40656,3652286ee39c2713d06cf286b932f9c88f1a0794@65.108.235.209:18656,87cec7782444e2863f57c94b66c058f3533ffd60@158.220.106.208:26656,9d9d7a060cdf621b275c5127e736ad25f381eb6b@95.214.52.138:25676,e052b7c899bae41f6d89f70f81de50e28b72a7bf@38.242.237.100:26656,9dc1f555bd37d6840237f32a2cd4d79ba1c80cb5@65.108.227.112:31656,aa020c9fdae827a4f912b77786d8080c62a34857@37.214.208.178:27556,f35a6ea4693d24d3727a8e866acab2a9faa2ddbc@91.223.3.144:26256,a1e49118b82bbd2b0a8c9943db00e02c5f0a3e57@83.59.175.230:26656,1786d7d18b39d5824cae23e8085c87883ed661e6@65.109.147.57:36656,5c2a752c9b1952dbed075c56c600c3a79b58c395@95.214.52.139:27226,1b5c5927e6e3685b3e9fc278ca4c9d7002d4cc10@65.21.134.250:17656,9d6ff8ca3c73ab08b7fcd59f47ed9cf7bd80f14e@185.217.126.187:36656,4640b6c775c05b6146a708a3b5ec2241c1688588@161.97.147.255:50656,b33500a3aaeb7fa116bdbddbe9c91c3158f38f8d@128.199.18.172:26656,a9bcb95ee047c4a909c675dc36c556eafe1248e1@195.201.174.109:46656,0a54815282d06cd10ce30b5ba3f9721c6ca1b600@135.181.33.42:50656,4f3ae90ab38c9c327654084a4f1ac9a89b097fc3@51.81.208.203:26656,2c40b0aedc41b7c1b20c7c243dd5edd698428c41@138.201.85.176:26696,9ea0473b3684dbf1f2cf194f69f746566dab6760@78.46.99.50:22656,4c66c9cd1bd73a2e8ff7fde84292850f9002efdc@65.109.92.148:26656,a876f7cda5f1ddd16aa271ec43cba750c0ba32c4@77.37.176.99:26656,cb706ebe1d7a1f1d3e281bf46a78d84251f50810@95.217.58.111:26656,030c769628e3e56928f8fd143ce9bd9ce53dba34@31.220.85.212:46656,d1c5c6bf4641d1800e931af6858275f08c20706d@23.88.5.169:18656,cf2de6fcee7dd1e7bbe3413e9c182481f49eede0@65.108.9.164:21656,371f313df7f79b34d65f026769a3e0c3e77127eb@45.137.67.238:26656"
sed -i 's|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.ojo/config/config.toml

seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.ojo/config/config.toml

# Set up filter for "bad" peers
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.ojo/config/config.toml

#
sed -i -e "s/^timeout_commit *=.*/timeout_commit = \"10s\"/" $HOME/.ojo/config/config.toml
sed -i -e "s/^timeout_propose *=.*/timeout_propose = \"10s\"/" $HOME/.ojo/config/config.toml
sed -i -e "s/^create_empty_blocks_interval *=.*/create_empty_blocks_interval = \"10s\"/" $HOME/.ojo/config/config.toml
sed -i 's/create_empty_blocks = .*/create_empty_blocks = true/g' ~/.ojo/config/config.toml
sed -i 's/timeout_broadcast_tx_commit = ".*s"/timeout_broadcast_tx_commit = "10s"/g' ~/.ojo/config/config.tom

# Set up pruning
pruning="custom"
pruning_keep_recent="1000"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.ojo/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.ojo/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.ojo/config/app.toml
```

***(OPTIONAL) Turn off indexing in config.toml***
```bash
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.ojo/config/config.toml
```

***Cosmovisor***
```bash
# Install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest
```
```bash
# Create directories
mkdir -p ~/.ojo/cosmovisor
mkdir -p ~/.ojo/cosmovisor/genesis
mkdir -p ~/.ojo/cosmovisor/genesis/bin
mkdir -p ~/.ojo/cosmovisor/upgrades
```
```bash
# Copy the binary file to the cosmovisor folder
cp `which ojod` ~/.ojo/cosmovisor/genesis/bin/ojod
```
```bash
# Create service file (One command)
sudo tee /etc/systemd/system/ojod.service > /dev/null <<EOF
[Unit]
Description=ojod daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start --x-crisis-skip-assert-invariants
Restart=always
RestartSec=3
LimitNOFILE=infinity

Environment="DAEMON_NAME=ojod"
Environment="DAEMON_HOME=${HOME}/.ojo"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"

[Install]
WantedBy=multi-user.target
EOF
```
```bash
# Start the node
systemctl daemon-reload
systemctl enable ojod
systemctl restart ojod && journalctl -u ojod -f -o cat

# Escape from logs ctrl+c
```
```bash
# Check the logs again
journalctl -u ojod -f -o cat

# Escape from logs ctrl+c
```

Now all ok, Check status
```bash
ojod status 2>&1 | jq "{catching_up: .SyncInfo.catching_up}"
"catching_up": false means that the node is synchronized, we are waiting for complete synchronization
```

***Wallets***
```bash
# Create wallet
ojod keys add wallet
```

Create a password for the wallet and write it down so you don't forget it. The wallet has been created. In the last line there will be a phrase that must be written down
```bash
# If the wallet was already there, restore it
ojod keys add wallet --recover
# Insert the seed phrase from your wallet
# If everything is correct, you will see your wallet data
```

Go to the # faucet branch and request tokens
```bash
# Save the wallet address
WALLET_OJO=$(ojod keys show wallet -a)
VALOPER_OJO=$(ojod keys show wallet --bech val -a)
echo "export WALLET_OJO="${WALLET_OJO} >> $HOME/.bash_profile
echo "export VALOPER_OJO="${VALOPER_OJO} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
# Check the ballance
ojod q bank balances $WALLET_OJO
```

***Validator***

Do not forget to create a profile on https://keybase.io/ and set a profile photo there that will be imported by key and used for your validators.
```bash
# Change <identity> to your key from keybase
ojod tx staking create-validator \
--amount 1000000uojo \
--from=wallet \
--commission-rate "0.05" \
--commission-max-rate "0.20" \
--commission-max-change-rate "0.1" \
--min-self-delegation "1" \
--pubkey=$(ojod tendermint show-validator) \
--moniker=$MONIKER_OJO \
--chain-id=$CHAIN_ID_OJO \
--fees=5000uojo \
--identity=<identity> \
--details="" \
--website="" \
-y
```

Check yourself in the list explorer

Or by command
```bash
ojod query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="$MONIKER_OJO")' | jq
```
```bash
# Edit the validator
ojod tx staking edit-validator \
  --new-moniker=$MONIKER_OJO \
  --website="" \
  --identity=<identity> \
  --details="" \
  --chain-id=$CHAIN_ID_OJO \
  --fees=1000uojo \
  --from=wallet
```
!!! Save priv_validator_key.json which located in /root/.ojo/config

**Price Feeder**
```bash
cd $HOME
git clone https://github.com/ojo-network/price-feeder && cd price-feeder
git checkout v0.1.1
make install

price-feeder version
# version: HEAD-5d46ed438d33d7904c0d947ebc6a3dd48ce0de59
# commit: 5d46ed438d33d7904c0d947ebc6a3dd48ce0de59
# sdk: v0.46.7
# go: go1.19.4 linux/amd64
```
```bash
mkdir -p $HOME/ojo_price-feeder
wget -O $HOME/ojo_price-feeder/price-feeder.toml "https://raw.githubusercontent.com/ojo-network/price-feeder/main/price-feeder.example.toml"
```
```bash
# Create separate wallet
ojod keys add OJO_FEEDER_ADDR --keyring-backend os
```
```bash
#Enter dependency
PASS=<your_password>
OJO_FEEDER_ADDR=<ojo1jkg...>
```
```bash
#Add PASS to config
sed -i '/^dir *=.*/a pass = ""' $HOME/ojo_price-feeder/price-feeder.toml
```
```bash
#Set the config
sed -i "s/^address *=.*/address= \"$WALLET_OJO\"/;\
s/^chain_id *=.*/chain_id= \"$CHAIN_ID_OJO\"/;\
s/^validator *=.*/validator = \"$VALOPER_OJO\"/;\
s/^backend *=.*/backend = \"os\"/;\
s|^dir *=.*|dir = \"$HOME/.ojo\"|;\
s|^pass *=.*|pass = \"$PASS\"|;\
s|^grpc_endpoint *=.*|grpc_endpoint = \"localhost:${PORT_OJO}90\"|;\
s|^tmrpc_endpoint *=.*|tmrpc_endpoint = \"http://localhost:${PORT_OJO}657\"|;" $HOME/ojo_price-feeder/price-feeder.toml
```
```bash
# Send tokens for fees (around 1000 tokens)
ojod tx bank send wallet $OJO_FEEDER_ADDR 100000000uojo --fees 7500uojo -y
```
```bash
# Check balance
nibid q bank balances $OJO_FEEDER_ADDR
```
```bash
# Create service file
tee /etc/systemd/system/price-feeder.service > /dev/null <<EOF
[Unit]
Description=OJO PFD
After=network.target
[Service]
User=$USER
Environment="PRICE_FEEDER_PASS=$PASS"
Type=simple
ExecStart=$(which price-feeder) $HOME/ojo_price-feeder/price-feeder.toml --log-level debug
RestartSec=10
Restart=on-failure
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```
```bash
ojod tx oracle delegate-feed-consent $WALLET_OJO $OJO_FEEDER_ADDR --fees 40000uojo -y
sed -i "s/^address *=.*/address= \"$OJO_FEEDER_ADDR\"/" $HOME/ojo_price-feeder/price-feeder.toml
systemctl daemon-reload
systemctl enable price-feeder
systemctl restart price-feeder && journalctl -u price-feeder -f -o cat

# Exit from logs ctrl+c
```
