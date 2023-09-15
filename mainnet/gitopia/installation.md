# Installation

_**Automatic Installation**_

```bash
# Soon...
```

_**Manual Installation**_

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
if [ "$(go version)" != "go version go1.20.2 linux/amd64" ]; then \
ver="1.20.2" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile ; \
fi

go version

# go version go1.20.2 linux/amd64
```

```bash
# Set the variables

# Come up with the name of your node and replace it instead <your_moniker>
GITOPIA_NODE_NAME=<your_node_name>

echo 'export GITOPIA_NODE_NAME='$GITOPIA_NODE_NAME >> $HOME/.bash_profile
echo "export GITOPIA_CHAIN_ID=gitopia" >> $HOME/.bash_profile
echo "export GITOPIA_PORT=10" >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```

```bash
# Download binary files
cd $HOME
git clone https://github.com/gitopia/gitopia && cd gitopia
git checkout v3.0.1
make install
sudo cp $HOME/go/bin/gitopiad /usr/local/bin/gitopiad

gitopiad version --long | grep -e version -e commit

# version: 3.0.1
```

```bash
# Initialize the node
gitopiad init $GITOPIA_NODE_NAME --chain-id $GITOPIA_CHAIN_ID
```

```bash
# Download Genesis
wget -O genesis.json https://ss.nodeist.net/gitopia/genesis.json --inet4-only
mv genesis.json ~/.gitopia/config

# Check Genesis
sha256sum ~/.gitopia/config/genesis.json 
# 0cf5c55e6ea1fbcebccadba0f6dc0b83ac76d1b608487a06978956404ce33e66
```

```bash
# Set the ports

# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${GITOPIA_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${GITOPIA_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${GITOPIA_PORT}061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${GITOPIA_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${GITOPIA_PORT}660\"%" $HOME/.gitopia/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${GITOPIA_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${GITOPIA_PORT}91\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1${GITOPIA_PORT}7\"%" $HOME/.gitopia/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${GITOPIA_PORT}657\"%" $HOME/.gitopia/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:${GITOPIA_PORT}656\"/" $HOME/.gitopia/config/config.toml
```

**Setup config**

```bash
# correct config (so we can no longer use the chain-id flag for every CLI command in client.toml)
gitopiad config chain-id $GITOPIA_CHAIN_ID

# adjust if necessary keyring-backend в client.toml 
gitopiad config keyring-backend os

gitopiad config node tcp://localhost:${GITOPIA_PORT}657

# Set the minimum price for gas
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.001ulore\"/" $HOME/.gitopia/config/app.toml

# Add seeds/peers в config.toml
PEERS="4cf66531681c92f15c95c25bd1bff524f9dca35e@65.109.154.181:26656,b2f764694d52e09793d68259d584ece0c194b6fe@65.108.229.93:26656,082e95b5d5351e68dcfb24dff802f9064cfd5a4c@65.109.92.241:51056,a94aec7233f9fec2b2de4b5c9dab6ad979820b3d@65.109.104.118:60756,a0ebd1e5845148c47451452047c7c99621da195e@65.109.96.93:60556,4adfa5889675e1e91ea4459e15ff4a0ba53e7828@65.108.224.156:19656,12f6b84a23b054a6591c647c2a4456c40af65cce@5.9.147.22:24657,88497ab3bbbcc1e8545771f45020e738bcce590f@95.165.89.222:24136,abca18ed112719b4f0a23932797dba2733f0fd44@23.88.5.169:25656,976d95adec7f0d7fda4464df019fa538fa0bb4ce@144.76.97.251:44656,ffd761a9e0d86609de6dae5935f99451694051a9@34.28.130.17:26656,5b2df98ad73a0a81a5bd31da4489a9236a7d7a99@65.21.91.160:26867,712dd67b7abe08577d394e90a4930492c8f7d2ee@65.108.124.219:41656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.gitopia/config/config.toml

seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.gitopia/config/config.toml

# Set up filter for "bad" peers
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.gitopia/config/config.toml

#
sed -i -e "s/^timeout_commit *=.*/timeout_commit = \"60s\"/" $HOME/.gitopia/config/config.toml
sed -i -e "s/^timeout_propose *=.*/timeout_propose = \"60s\"/" $HOME/.gitopia/config/config.toml
sed -i -e "s/^create_empty_blocks_interval *=.*/create_empty_blocks_interval = \"60s\"/" $HOME/.gitopia/config/config.toml
sed -i 's/create_empty_blocks = .*/create_empty_blocks = true/g' ~/.gitopia/config/config.toml
sed -i 's/timeout_broadcast_tx_commit = ".*s"/timeout_broadcast_tx_commit = "601s"/g' ~/.gitopia/config/config.toml

# Set up pruning
pruning="custom"
pruning_keep_recent="1000"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.gitopia/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.gitopia/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.gitopia/config/app.toml

```

**(OPTIONAL) Turn off indexing in config.toml**

```bash
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.gitopia/config/config.toml
```

**Cosmovisor**

```bash
# Install Cosmovisor
go install github.com/cosmos/cosmos-sdk/cosmovisor/cmd/cosmovisor@v1.0.0
```

```bash
# Create Cosmovisor Folders
mkdir -p ~/.gitopia/cosmovisor/genesis/bin
mkdir -p ~/.gitopia/cosmovisor/upgrades

# Load Node Binary into Cosmovisor Folder
cp ~/go/bin/gitopiad ~/.gitopia/cosmovisor/genesis/bin
```

_**SERVICE FILE**_

```bash
# Create service file (One command)
sudo tee /etc/systemd/system/gitopiad.service > /dev/null <<EOF
[Unit]
Description="gitopiad node"
After=network-online.target

[Service]
User=$USER
ExecStart=$HOME/go/bin/cosmovisor start
Restart=always
RestartSec=3
LimitNOFILE=4096
Environment="DAEMON_NAME=gitopiad"
Environment="DAEMON_HOME=$HOME/.gitopia"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="UNSAFE_SKIP_BACKUP=true"

[Install]
WantedBy=multi-user.target
EOF
```

```bash
# Start the node
systemctl daemon-reload
systemctl enable gitopiad
systemctl restart gitopiad && journalctl -u gitopiad -f -o cat

# Escape from logs ctrl+c
```

```bash
# Check the logs again
journalctl -u gitopiad -f -o cat

# Escape from logs ctrl+c
```

```bash
# Check status
gitopiad status 2>&1 | jq .SyncInfo

# "catching_up": false means that the node is synchronized, we are waiting for complete synchronization
```

**Wallets**

```bash
# Create wallet
gitopiad keys add wallet
# Create a password for the wallet and write it down so you don't forget it. The wallet has been created. In the last line there will be a phrase that must be written down
```

```bash
# If the wallet was already there, restore it
gitopiad keys add wallet --recover
# Insert the seed phrase from your wallet
# If everything is correct, you will see your wallet data
```

Go to the # faucet branch and request tokens

```bash
# Save the wallet address
GITOPIA_ADDRESS=$(gitopiad keys show wallet -a)

GITOPIA_VALOPER=$(gitopiad keys show wallet --bech val -a)

echo "export GITOPIA_ADDRESS="${GITOPIA_ADDRESS} >> $HOME/.bash_profile
echo "export GITOPIA_VALOPER="${GITOPIA_VALOPER} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
# Check the ballance
gitopiad q bank balances $GITOPIA_ADDRESS
```

_**Validator**_

Do not forget to create a profile on https://keybase.io/ and set a profile photo there that will be imported by key and used for your validators.

```bash
# Change <identity> to your key from keybase
gitopiad tx staking create-validator \
--amount 1000000ulore \
--from=wallet \
--commission-rate "0.05" \
--commission-max-rate "0.20" \
--commission-max-change-rate "0.1" \
--min-self-delegation "1" \
--pubkey=$(gitopiad tendermint show-validator) \
--moniker=$GITOPIA_NODE_NAME \
--chain-id=$GITOPIA_CHAIN_ID \
--identity=<identity> \
--details="" \
--website="" \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.025ulore \
-y
```

Check yourself in the list ecsplorer

Or by command

```bash
gitopiad query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="$GITOPIA_NODE_NAME")' | jq
```

```bash
# Edit the validator
gitopiad tx staking edit-validator \
  --new-moniker=$GITOPIA_NODE_NAME \
  --website="" \
  --identity=<identity> \
  --details="" \
  --chain-id=$GITOPIA_CHAIN_ID \
  --fees=5000ulore \
  --from=wallet
```

!!! Save priv\_validator\_key.json which is located in /root/.gitopiad/config
