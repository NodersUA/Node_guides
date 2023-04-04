***Automatic Installation***
```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/Andromeda)
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
MONIKER_ANDROMEDA=<your_moniker>

echo 'export MONIKER_ANDROMEDA='$MONIKER_ANDROMEDA >> $HOME/.bash_profile
echo "export CHAIN_ID_ANDROMEDA=galileo-3" >> $HOME/.bash_profile
echo "export PORT_ANDROMEDA=15" >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```
```bash
# Download binary files
cd $HOME 
git clone https://github.com/andromedaprotocol/andromedad.git
cd andromedad 
git fetch --all 
git checkout galileo-3-v1.1.0-beta1
make install
sudo cp $HOME/go/bin/andromedad /usr/local/bin/andromedad
andromedad version --long | grep -e version -e commit
# galileo-3-v1.1.0-beta1
```
```bash
# Initialize the node
andromedad init $MONIKER_ANDROMEDA --chain-id $CHAIN_ID_ANDROMEDA
```
```bash
# Download Genesis
wget -O $HOME/.andromedad/config/genesis.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/AndromedaProtocol/genesis.json"

# Check Genesis
sha256sum $HOME/.andromedad/config/genesis.json
# 26cefef408b9cdc013e7427d5fb05bbd44a006d80b7e0db36aaf125edda9b4e6
```
```bash
# Set the ports

# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${PORT_ANDROMEDA}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${PORT_ANDROMEDA}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${PORT_ANDROMEDA}061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${PORT_ANDROMEDA}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${PORT_ANDROMEDA}660\"%" $HOME/.andromedad/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${PORT_ANDROMEDA}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${PORT_ANDROMEDA}91\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1${PORT_ANDROMEDA}7\"%" $HOME/.andromedad/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${PORT_ANDROMEDA}657\"%" $HOME/.andromedad/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:${PORT_ANDROMEDA}656\"/" $HOME/.andromedad/config/config.toml
```

***Setup config***
```bash
# correct config (so we can no longer use the chain-id flag for every CLI command in client.toml)
andromedad config chain-id galileo-3

# adjust if necessary keyring-backend в client.toml 
andromedad config keyring-backend os

andromedad config node tcp://localhost:${PORT_ANDROMEDA}657

# Set the minimum price for gas
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025uandr\"/;" ~/.andromedad/config/app.toml

# Add seeds/peers в config.toml
peers=""
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.andromedad/config/config.toml

seeds="3a445bfdbe2d0c8ee82461633aa3af31bc2b4dc0@prod-pnet-seed-node.lavanet.xyz:26656,e593c7a9ca61f5616119d6beb5bd8ef5dd28d62d@prod-pnet-seed-node2.lavanet.xyz:26656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.andromedad/config/config.toml

# Set up filter for "bad" peers
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.andromedad/config/config.toml

#
sed -i -e "s/^timeout_commit *=.*/timeout_commit = \"60s\"/" $HOME/.andromedad/config/config.toml
sed -i -e "s/^timeout_propose *=.*/timeout_propose = \"60s\"/" $HOME/.andromedad/config/config.toml
sed -i -e "s/^create_empty_blocks_interval *=.*/create_empty_blocks_interval = \"60s\"/" $HOME/.andromedad/config/config.toml
sed -i 's/create_empty_blocks = .*/create_empty_blocks = true/g' ~/.andromedad/config/config.toml
sed -i 's/timeout_broadcast_tx_commit = ".*s"/timeout_broadcast_tx_commit = "601s"/g' ~/.andromedad/config/config.toml

# Set up pruning
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.andromedad/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.andromedad/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.andromedad/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.andromedad/config/app.toml
```

***(OPTIONAL) Turn off indexing in config.toml***
```bash
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.andromedad/config/config.toml
```

***Cosmovisor***
```bash
# Install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest
```
```bash
# Create directories
mkdir -p ~/.andromedad/cosmovisor
mkdir -p ~/.andromedad/cosmovisor/genesis
mkdir -p ~/.andromedad/cosmovisor/genesis/bin
mkdir -p ~/.andromedad/cosmovisor/upgrades
```
```bash
# Copy the binary file to the cosmovisor folder
cp `which andromedad` ~/.andromedad/cosmovisor/genesis/bin/andromedad
```
```bash
# Create service file (One command)
sudo tee /etc/systemd/system/andromedad.service > /dev/null <<EOF
[Unit]
Description=andromeda daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start --x-crisis-skip-assert-invariants
Restart=always
RestartSec=3
LimitNOFILE=infinity

Environment="DAEMON_NAME=andromedad"
Environment="DAEMON_HOME=${HOME}/.andromedad"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"

[Install]
WantedBy=multi-user.target
EOF
```
```bash
# Start the node
systemctl daemon-reload
systemctl enable andromedad
systemctl restart andromedad && journalctl -u andromedad -f -o cat

# Escape from logs ctrl+c
```
```bash
# Check the logs again
journalctl -u andromedad -f -o cat

# Escape from logs ctrl+c
```

***Now all ok, Check status***
```bash
andromedad status 2>&1 | jq "{catching_up: .SyncInfo.catching_up}"
"catching_up": false означає, що нода синхронізована, чекаємо повної синхронізації
```

***Wallets***
```bash
# Create wallet
andromedad keys add wallet
```
Create a password for the wallet and write it down so you don't forget it. The wallet has been created. In the last line there will be a phrase that must be written down
```bash
# If the wallet was already there, restore it
andromedad keys add wallet --recover
# Insert the seed phrase from your wallet
# If everything is correct, you will see your wallet data
```

***Go to the # faucet branch and request tokens***
```bash
# Save the wallet address
# Replace <your_address> with your wallet address
WALLET_ANDROMEDA=<your_address>
echo "export WALLET_ANDROMEDA="${WALLET_ANDROMEDA}"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
```bash
# Check the ballance
andromedad q bank balances $WALLET_ANDROMEDA
```

**Validator**

Do not forget to create a profile on https://keybase.io/ and set a profile photo there that will be imported by key and used for your validators.
```bash
# Change <identity> to your key from keybase
andromedad tx staking create-validator \
--amount 1000000uandr \
--from=wallet \
--commission-rate "0.15" \
--commission-max-rate "0.20" \
--commission-max-change-rate "0.1" \
--min-self-delegation "1" \
--pubkey=$(andromedad tendermint show-validator) \
--moniker=$MONIKER_ANDROMEDA \
--chain-id=$CHAIN_ID_ANDROMEDA \
--fees=1000uandr \
--identity=<identity> \
--details="" \
--website="" \
-y
```

Check yourself in the list explorer

Or by command
```bash
andromedad query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="$MONIKER_ANDROMEDA")' | jq
```
```bash
# Edit the validator
andromedad tx staking edit-validator \
  --new-moniker=$MONIKER_ANDROMEDA \
  --website="" \
  --identity=<identity> \
  --details="" \
  --chain-id=$CHAIN_ID_ANDROMEDA \
  --fees=1000uandr \
  --from=wallet
```
```bash  
# Save valoper_address in bash
# Change <your_valoper_address> to the address of the validator, starting with andrvaloper...
VALOPER_ANDROMEDA=<your_valoper_address>
echo "export VALOPER_ANDROMEDA="${VALOPER_ANDROMEDA}"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
***!!! Сохраняем priv_validator_key.json который находится в /root/.andromedad/config***

