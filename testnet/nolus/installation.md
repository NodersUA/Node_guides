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
MONIKER_NOLUS=<your_moniker>

echo 'export MONIKER_NOLUS='$MONIKER_NOLUS >> $HOME/.bash_profile
echo "export CHAIN_ID_NOLUS=nolus-rila" >> $HOME/.bash_profile
echo "export PORT_NOLUS=39" >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```
```bash
# Download binary files
cd $HOME 
git clone https://github.com/Nolus-Protocol/nolus-core
cd nolus-core
git fetch --all 
git checkout v0.2.1-testnet
make install
sudo cp $HOME/go/bin/nolusd /usr/local/bin/nolusd
nolusd version --long | grep -e version -e commit -e build
#version:v0.2.1-testnet
#commit:b33e872e7411d86549a4a8ac1ffbf9b1f5190efa
```
```bash
# Initialize the node
saod init $MONIKER_NOLUS --chain-id $CHAIN_ID_NOLUS
```
```bash
# Download Genesis
wget -O $HOME/.nolus/config/genesis.json "https://raw.githubusercontent.com/Nolus-Protocol/nolus-networks/main/testnet/nolus-rila/genesis.json"

# Check Genesis
sha256sum $HOME/.nolus/config/genesis.json
# d22ea6488afe58478c54afeb2d6b5a45622c797dfd75c91a8653eb1f094173c5
```
```bash
# Set the ports

# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${PORT_NOLUS}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${PORT_NOLUS}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${PORT_NOLUS}061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${PORT_NOLUS}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${PORT_NOLUS}660\"%" $HOME/.nolus/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${PORT_NOLUS}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${PORT_NOLUS}91\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${PORT_NOLUS}27\"%" $HOME/.nolus/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${PORT_NOLUS}657\"%" $HOME/.nolus/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:${PORT_NOLUS}656\"/" $HOME/.nolus/config/config.toml
```

***Setup config***
```bash
# correct config (so we can no longer use the chain-id flag for every CLI command in client.toml)
nolusd config chain-id nolus-rila

# adjust if necessary keyring-backend в client.toml 
nolusd config keyring-backend os

nolusd config node tcp://localhost:${PORT_NOLUS}657

# Set the minimum price for gas
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025unls\"/;" ~/.nolus/config/app.toml

# Add seeds/peers в config.toml
peers="56cee116ac477689df3b4d86cea5e49cfb450dda@54.246.232.38:26656,56f14005119e17ffb4ef3091886e6f7efd375bfd@34.241.107.0:26656,7f26067679b4323496319fda007a279b52387d77@63.35.222.83:26656,7f4a1876560d807bb049b2e0d0aa4c60cc83aa0a@63.32.88.49:26656,3889ba7efc588b6ec6bdef55a7295f3dd559ebd7@3.249.209.26:26656,de7b54f988a5d086656dcb588f079eb7367f6033@34.244.137.169:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.nolus/config/config.toml

seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.nolus/config/config.toml

# Set up filter for "bad" peers
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.nolus/config/config.toml

# Consensus
sed -i -e "s/^timeout_commit *=.*/timeout_commit = \"10s\"/" $HOME/.nolus/config/config.toml
sed -i -e "s/^timeout_propose *=.*/timeout_propose = \"10s\"/" $HOME/.nolus/config/config.toml
sed -i -e "s/^create_empty_blocks_interval *=.*/create_empty_blocks_interval = \"10s\"/" $HOME/.nolus/config/config.toml
sed -i 's/create_empty_blocks = .*/create_empty_blocks = true/g' ~/.nolus/config/config.toml
sed -i 's/timeout_broadcast_tx_commit = ".*s"/timeout_broadcast_tx_commit = "10s"/g' ~/.nolus/config/config.tom

# Set up pruning
pruning="custom"
pruning_keep_recent="1000"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.nolus/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.nolus/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.nolus/config/app.toml
```

***(OPTIONAL) Turn off indexing in config.toml***
```bash
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.nolus/config/config.toml
```

***Cosmovisor***
```bash
# Install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest
```
```bash
# Create directories
mkdir -p ~/.nolus/cosmovisor
mkdir -p ~/.nolus/cosmovisor/genesis
mkdir -p ~/.nolus/cosmovisor/genesis/bin
mkdir -p ~/.nolus/cosmovisor/upgrades
```
```bash
# Copy the binary file to the cosmovisor folder
cp `which nolusd` ~/.nolus/cosmovisor/genesis/bin/nolusd
```
```bash
# Create service file (One command)
sudo tee /etc/systemd/system/nolusd.service > /dev/null <<EOF
[Unit]
Description=nolusd daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start --x-crisis-skip-assert-invariants
Restart=always
RestartSec=3
LimitNOFILE=infinity

Environment="DAEMON_NAME=nolusd"
Environment="DAEMON_HOME=${HOME}/.nolus"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"

[Install]
WantedBy=multi-user.target
EOF
```
```bash
# Start the node
systemctl daemon-reload
systemctl enable nolusd
systemctl restart nolusd && journalctl -u nolusd -f -o cat

# Escape from logs ctrl+c
```
```bash
# Check the logs again
journalctl -u nolusd -f -o cat

# Escape from logs ctrl+c
```

Now all ok, Check status
```bash
nolusd status 2>&1 | jq "{catching_up: .SyncInfo.catching_up}"
"catching_up": false means that the node is synchronized, we are waiting for complete synchronization
```

***Wallets***
```bash
# Create wallet
nolusd keys add wallet
```

Create a password for the wallet and write it down so you don't forget it. The wallet has been created. In the last line there will be a phrase that must be written down
```bash
# If the wallet was already there, restore it
nolusd keys add wallet --recover
# Insert the seed phrase from your wallet
# If everything is correct, you will see your wallet data
```

Go to the # faucet branch and request tokens
```bash
# Save the wallet address
# Replace <your_address> with your wallet address
WALLET_NOLUS=<your_address>
echo "export WALLET_NOLUS="${WALLET_NOLUS}"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
```bash
# Check the ballance
nolusd q bank balances $WALLET_NOLUS
```

***Validator***

Do not forget to create a profile on https://keybase.io/ and set a profile photo there that will be imported by key and used for your validators.
```bash
# Change <identity> to your key from keybase
nolusd tx staking create-validator \
--amount 1000000sunls \
--from=wallet \
--commission-rate "0.05" \
--commission-max-rate "0.20" \
--commission-max-change-rate "0.1" \
--min-self-delegation "1" \
--pubkey=$(nolusd tendermint show-validator) \
--moniker=$MONIKER_NOLUS \
--chain-id=$CHAIN_ID_NOLUS \
--fees=5000unls \
--identity=<identity> \
--details="" \
--website="" \
-y
```

Check yourself in the list explorer

Or by command
```bash
nolusd query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="$MONIKER_NOLUS")' | jq
```
```bash
# Edit the validator
nolusd tx staking edit-validator \
  --new-moniker=$MONIKER_NOLUS \
  --website="" \
  --identity=<identity> \
  --details="" \
  --chain-id=$CHAIN_ID_NOLUS \
  --fees=1000unls \
  --from=wallet
```
```bash
# Save valoper_address in bash
# Change <your_valoper_address> to the address of the validator, starting with andrvaloper...
VALOPER_NOLUS=<your_valoper_address>
echo "export VALOPER_NOLUS="${VALOPER_NOLUS}"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
!!! Save priv_validator_key.json which located in /root/.nolus/config
