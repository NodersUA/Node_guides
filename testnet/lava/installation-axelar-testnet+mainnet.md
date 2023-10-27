# Installation (Axelar-testnet+mainnet)

## _**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/axelar)
```

## **Manual Installation**

```bash
# Update the repositories
apt update && apt upgrade -y
```

```bash
# Install developer packages
sudo apt -qy install curl git jq lz4 build-essential
```

```bash
#INSTALL GO
if [ "$(go version)" != "go version go1.20.5 linux/amd64" ]; then \
ver="1.20.5" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile ; \
fi
go version
```

```bash
# Set the variables

# Come up with the name of your node and replace it instead <your_moniker>
MONIKER=<your_moniker>

echo 'export MONIKER='$MONIKER >> $HOME/.bash_profile
echo "export AXELAR_TESTNET_CHAIN_ID=axelar-testnet-lisbon-3" >> $HOME/.bash_profile
echo "export AXELAR_MAINNET_CHAIN_ID=axelar-dojo-1" >> $HOME/.bash_profile
echo "export AXELAR_TESTNET_PORT=40" >> $HOME/.bash_profile
echo "export AXELAR_MAINNET_PORT=41" >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```

```bash
# Download binary files
# Clone project repository
cd $HOME
rm -rf axelar-core
git clone https://github.com/axelarnetwork/axelar-core.git
cd axelar-core
git checkout v0.34.1

go mod edit -replace github.com/tendermint/tm-db=github.com/notional-labs/tm-db@v0.6.8-pebble
go mod tidy
go mod edit -replace github.com/cometbft/cometbft-db=github.com/notional-labs/cometbft-db@pebble
go mod tidy

go install -ldflags "-w -s -X github.com/cosmos/cosmos-sdk/types.DBBackend=pebbledb \
 -X github.com/cosmos/cosmos-sdk/version.Version=$(git describe --tags)-pebbledb \
 -X github.com/cosmos/cosmos-sdk/version.Commit=$(git log -1 --format='%H')" -tags pebbledb ./...

sudo cp $(which axelard) /usr/local/bin/t_axelar
sudo cp $(which axelard) /usr/local/bin/m_axelar

cd $HOME
t_axelar version
m_axelar version
# 0.34.1
```

```bash
cp -r ~/.axelar/ ~/.axelar_testnet/
cp -r ~/.axelar/ ~/.axelar_mainnet/
rm -rf ~/.axelar/
```

### Testnet

```bash
# Initialize the node
t_axelar init $MONIKER --chain-id $AXELAR_TESTNET_CHAIN_ID --home $HOME/.axelar_testnet
```

```bash
# Download Genesis
curl -Ls http://snapshots.stakevillage.net/snapshots/axelar-testnet-lisbon-3/genesis.json > $HOME/.axelar_testnet/config/genesis.json
```

```bash
# Set the ports

# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${AXELAR_TESTNET_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${AXELAR_TESTNET_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${AXELAR_TESTNET_PORT}061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${AXELAR_TESTNET_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${AXELAR_TESTNET_PORT}660\"%" $HOME/.axelar_testnet/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${AXELAR_TESTNET_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${AXELAR_TESTNET_PORT}91\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1${AXELAR_TESTNET_PORT}7\"%" $HOME/.axelar_testnet/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${AXELAR_TESTNET_PORT}657\"%" $HOME/.axelar_testnet/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:${AXELAR_TESTNET_PORT}656\"/" $HOME/.axelar_testnet/config/config.toml

```

```bash
# Set the minimum price for gas
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.007uaxl\"/" ~/.axelar_testnet/config/app.toml

# Add seeds/peers в config.toml
peers="d5519e378247dfb61dfe90652d1fe3e2b3005a5b@axelar-testnet.rpc.kjnodes.com:16556"
sed -i -e "s|^persistent_peers *=.*|persistent_peers = \"$peers\"|" $HOME/.axelar_testnet/config/config.toml
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.axelar_testnet/config/config.toml

seeds="3f472746f46493309650e5a033076689996c8881@axelar-testnet.rpc.kjnodes.com:16559"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.axelar_testnet/config/config.toml

# Set up filter for "bad" peers
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.axelar_testnet/config/config.toml

# prunning
pruning="custom"
pruning_keep_recent="50000"
pruning_keep_every="0"
pruning_interval="19"

sed -i "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.axelar_testnet/config/app.toml
sed -i "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.axelar_testnet/config/app.toml
sed -i "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.axelar_testnet/config/app.toml
sed -i "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.axelar_testnet/config/app.toml

# set pebbledb
db_backend="pebbledb"
sed -i "s/^db_backend *=.*/db_backend = \"$db_backend\"/" $HOME/.axelar_testnet/config/config.toml
sed -i "s/^app-db-backend *=.*/app-db-backend = \"$db_backend\"/" $HOME/.axelar_testnet/config/app.toml
```

```bash
# Download latest chain snapshot
curl -L http://snapshots.stakevillage.net/snapshots/axelar-testnet-lisbon-3/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.axelar_testnet
```

```bash
# Create service file (One command)
tee /etc/systemd/system/t_axelar.service > /dev/null << EOF
[Unit]
Description=Axelar testnet (PebbleDB) node service
After=network.target
[Service]
Type=simple
User=$USER
ExecStart=$(which t_axelar) start --home $HOME/.axelar_testnet
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

```bash
# Start the node
systemctl daemon-reload
systemctl enable t_axelar
systemctl restart t_axelar && journalctl -u t_axelar -f -o cat

# Escape from logs ctrl+c
```

```bash
# Check the logs again
journalctl -u t_axelar -f -o cat

# Escape from logs ctrl+c
```

**Wallet**

```bash
# Create wallet
t_axelar keys add wallet --home $HOME/.axelar_testnet
```

The wallet has been created. In the last line there will be a phrase that must be written down

```bash
# If the wallet was already there, restore it
t_axelar keys add wallet --recover --home $HOME/.axelar_testnet
# Insert the seed phrase from your wallet
# If everything is correct, you will see your wallet data
```

```bash
# Save the wallet address
AXELAR_TESTNET_ADDRESS=$(t_axelar keys show wallet -a --home $HOME/.axelar_testnet)
```

```bash
echo "export AXELAR_TESTNET_ADDRESS="${AXELAR_TESTNET_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

Fill out the [form](https://lavanet.typeform.com/to/iW8rynWg?utm\_source=providers-guide-to-axelar-iprpc\&utm\_medium=blog\&utm\_campaign=axelar-iprpc-provider\&typeform-source=www.lavanet.xyz)

### Mainnet

```bash
# Initialize the node
m_axelar init $MONIKER --chain-id $AXELAR_MAINNET_CHAIN_ID --home $HOME/.axelar_mainnet
```

```bash
# Download Genesis
curl -Ls http://snapshots.stakevillage.net/snapshots/axelar-dojo-1/genesis.json > $HOME/.axelar_mainnet/config/genesis.json
```

```bash
# Set the ports

# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${AXELAR_MAINNET_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${AXELAR_MAINNET_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${AXELAR_MAINNET_PORT}061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${AXELAR_MAINNET_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${AXELAR_MAINNET_PORT}660\"%" $HOME/.axelar_mainnet/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${AXELAR_MAINNET_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${AXELAR_MAINNET_PORT}91\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1${AXELAR_MAINNET_PORT}7\"%" $HOME/.axelar_mainnet/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${AXELAR_MAINNET_PORT}657\"%" $HOME/.axelar_mainnet/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:${AXELAR_MAINNET_PORT}656\"/" $HOME/.axelar_mainnet/config/config.toml
```

```bash
# Set the minimum price for gas
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.007uaxl\"/" ~/.axelar_mainnet/config/app.toml

# Add seeds/peers в config.toml
peers="0c9555d92aaa7cda65d3456ef1a6ac6f24e055d5@65.21.123.54:26676,69266a777c64b9533403add14fb724e2eebfd848@65.108.122.247:26656,609504b517f88f628e98d4a918ffc69e9654b451@65.108.192.147:26656,5b262497ee26cb079766e53f1a74c8e8a0f5cf7a@65.109.52.178:26656,3d67d0646cddcc203b41434aceea64ade22ba6fc@18.217.111.172:26656,6acf3b257c4d839f4f2eb077eeb9a758ba4865e7@176.103.222.161:26656,5622ce45be98fc4f54a295ded742d7c7d7551285@65.108.200.49:27856,051a6fe084df02c6a4c484139899808eea841181@13.59.129.55:26656,4f12f80da0eda26c77f2780f755cea498198f8cd@3.142.113.84:26656,3bc24a2f1da2a3b0395e3b9b82b1507ae0bbce32@157.90.91.20:13656,5581d7215b95264e600209bfed0fa28a305620dd@3.134.196.244:26656,54e0c474ba49b1e78b09c9eff1a39ca3214c65a8@185.163.64.143:26656,f7061dc29a0ac18567848c1654e01b6a7a263051@51.158.156.171:36656,25255eaac0a7b4df8caa546a48d320d9cacd1f19@3.133.230.24:26656,803d85675bc116eee836d90a916a0a5f3d0d45fc@18.139.161.51:26656,dab362b5642b752f503ad066cb30f936fc0d2310@81.196.253.241:15656,a7b306b6421a0b474a0413905ffcae780d398ecd@65.108.232.134:53656,1136202f40f158b6e11257af1a34ce5379179d05@3.141.87.0:26656,57dc509d932efd27f378079c7959bcddd1131e90@136.243.174.45:56656,590a6723091c9f7049227b043bcbe84bdbcf3b57@198.244.165.175:15656,11f64e33e76755673705fba1db25a18059a333ab@65.21.74.228:26656,e6aeadba513e216954f7330834d0af0047fccce2@3.23.243.230:26656,1bd159908c6385a5c9767711c389cbe5d2027b1d@89.149.218.182:26656"
sed -i -e "s|^persistent_peers *=.*|persistent_peers = \"$peers\"|" $HOME/.axelar_mainnet/config/config.toml
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.axelar_mainnet/config/config.toml

seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.axelar_mainnet/config/config.toml

# Set up filter for "bad" peers
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.axelar_mainnet/config/config.toml

# prunning
pruning="custom"
pruning_keep_recent="50000"
pruning_keep_every="0"
pruning_interval="19"

sed -i "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.axelar_mainnet/config/app.toml
sed -i "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.axelar_mainnet/config/app.toml
sed -i "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.axelar_mainnet/config/app.toml
sed -i "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.axelar_mainnet/config/app.toml

# set pebbledb
db_backend="pebbledb"
sed -i "s/^db_backend *=.*/db_backend = \"$db_backend\"/" $HOME/.axelar_mainnet/config/config.toml
sed -i "s/^app-db-backend *=.*/app-db-backend = \"$db_backend\"/" $HOME/.axelar_mainnet/config/app.toml
```

```bash
# Download latest chain snapshot
curl -L http://snapshots.stakevillage.net/snapshots/axelar-dojo-1/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.axelar_mainnet
```

```bash
# Create service file (One command)
tee /etc/systemd/system/m_axelar.service > /dev/null << EOF
[Unit]
Description=Axelar testnet (PebbleDB) node service
After=network.target
[Service]
Type=simple
User=$USER
ExecStart=$(which m_axelar) start --home $HOME/.axelar_mainnet
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

```bash
# Start the node
systemctl daemon-reload
systemctl enable m_axelar
systemctl restart m_axelar && journalctl -u m_axelar -f -o cat

# Escape from logs ctrl+c
```

```bash
# Check the logs again
journalctl -u m_axelar -f -o cat

# Escape from logs ctrl+c
```

**Wallet**

```bash
# Create wallet
m_axelar keys add wallet --home $HOME/.axelar_mainnet
```

The wallet has been created. In the last line there will be a phrase that must be written down

```bash
# If the wallet was already there, restore it
m_axelar keys add wallet --recover --home $HOME/.axelar_mainnet
# Insert the seed phrase from your wallet
# If everything is correct, you will see your wallet data
```

```bash
# Save the wallet address
AXELAR_MAINNET_ADDRESS=$(m_axelar keys show wallet -a --home $HOME/.axelar_mainnet)
```

```bash
echo "export AXELAR_MAINNET_ADDRESS="${AXELAR_MAINNET_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

Fill out the [form](https://lavanet.typeform.com/to/iW8rynWg?utm\_source=providers-guide-to-axelar-iprpc\&utm\_medium=blog\&utm\_campaign=axelar-iprpc-provider\&typeform-source=www.lavanet.xyz)
