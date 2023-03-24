***Node***

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
ver="1.19.1" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version

# go version go1.19.1 linux/amd64
```
```bash
# Set the variables

# Come up with the name of your node and replace it instead <your_moniker>
GITOPIA_NODE_NAME=<your_node_name>

echo 'export GITOPIA_NODE_NAME='$GITOPIA_NODE_NAME >> $HOME/.bash_profile
echo "export GITOPIA_CHAIN_ID=gitopia-janus-testnet-2" >> $HOME/.bash_profile
echo "export GITOPIA_PORT=10" >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```
```bash
# Download binary files
cd $HOME
curl https://get.gitopia.com | bash
git clone -b v1.2.0 gitopia://gitopia/gitopia
cd gitopia && make install

gitopiad version --long | grep -e version -e commit

# version: 1.2.0 # commit: 64e4712aeae3c723346a365d67cf1dd3e91cc50c
```
```bash
# Initialize the node
gitopiad init $GITOPIA_NODE_NAME --chain-id $GITOPIA_CHAIN_ID
```
```bash
# Download Genesis
wget https://server.gitopia.com/raw/gitopia/testnets/master/gitopia-janus-testnet-2/genesis.json.gz
gunzip genesis.json.gz
cp genesis.json $HOME/.gitopia/config/genesis.json

# Check Genesis
sha256sum ~/.gitopia/config/genesis.json 
# 038a81d821f3d8f99e782cbfed609e4853d24843c48a1469287528e632a26162
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
```
# correct config (so we can no longer use the chain-id flag for every CLI command in client.toml)
gitopiad config chain-id $GITOPIA_CHAIN_ID

# adjust if necessary keyring-backend в client.toml 
gitopiad config keyring-backend os

gitopiad config node tcp://localhost:${GITOPIA_PORT}657

# Set the minimum price for gas
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.001utlore\"/" $HOME/.gitopia/config/app.toml

# Add seeds/peers в config.toml
peers=""
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.gitopia/config/config.toml

seeds="399d4e19186577b04c23296c4f7ecc53e61080cb@seed.gitopia.com:26656"
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

***Cosmovisor***

```bash
# Install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest
```
```bash
# Create directories
mkdir -p ~/.gitopia/cosmovisor
mkdir -p ~/.gitopia/cosmovisor/genesis
mkdir -p ~/.gitopia/cosmovisor/genesis/bin
mkdir -p ~/.gitopia/cosmovisor/upgrades
```
```bash
# Copy the binary file to the cosmovisor folder
cp `which gitopiad` ~/.gitopia/cosmovisor/genesis/bin/gitopiad
```
```bash
# Create service file (One command)
sudo tee /etc/systemd/system/gitopiad.service > /dev/null <<EOF
[Unit]
Description=gitopia daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start --x-crisis-skip-assert-invariants
Restart=always
RestartSec=3
LimitNOFILE=infinity

Environment="DAEMON_NAME=gitopiad"
Environment="DAEMON_HOME=${HOME}/.gitopiad"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"

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

"catching_up": false means that the node is synchronized, we are waiting for complete synchronization
```

**Wallets**

```bash
# Create wallet
gitopiad keys add wallet
Create a password for the wallet and write it down so you don't forget it. The wallet has been created. In the last line there will be a phrase that must be written down
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
# Replace <your_address> with your wallet address
GITOPIA_ADDRESS=<your_address>
echo "export GITOPIA_ADDRESS=$GITOPIA_ADDRESS" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
```bash
# Check the ballance
gitopiad q bank balances $GITOPIA_ADDRESS
```

***Validator***

Do not forget to create a profile on https://keybase.io/ and set a profile photo there that will be imported by key and used for your validators.

```bash
# Change <identity> to your key from keybase
gitopiad tx staking create-validator \
--amount 1000000utlore \
--from=$GITOPIA_WALLET_NAME \
--commission-rate "0.05" \
--commission-max-rate "0.20" \
--commission-max-change-rate "0.1" \
--min-self-delegation "1" \
--pubkey=$(gitopiad tendermint show-validator) \
--moniker=$GITOPIA_NODE_NAME \
--chain-id=$GITOPIA_CHAIN_ID \
--fees=5000utlore \
--identity="" \
--details="" \
--website="" \
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
  --fees=5000utlore \
  --from=$GITOPIA_WALLET_NAME
```
```bash
# Save valoper_address in bash
# Change <your_valoper_address> to the address of the validator, starting with nibivaloper...
GITOPIA_VALOPER=<your_valoper_address>
echo "export GITOPIA_VALOPER=$GITOPIA_VALOPER" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

!!! Save priv_validator_key.json which is located in /root/.gitopiad/config
