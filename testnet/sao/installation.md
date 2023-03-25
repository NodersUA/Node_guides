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
MONIKER_SAO=<your_moniker>

echo 'export MONIKER_SAO='$MONIKER_SAO >> $HOME/.bash_profile
echo "export CHAIN_ID_SAO=sao-testnet0" >> $HOME/.bash_profile
echo "export PORT_SAO=38" >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```
```bash
# Download binary files
cd $HOME 
git clone https://github.com/SaoNetwork/sao-consensus
cd sao-consensus
git fetch --all 
git checkout testnet0
make install
sudo cp $HOME/go/bin/saod /usr/local/bin/saod
saod version --long | grep -e version -e commit -e build
#version: 0.0.9-28-g284ebe6
#commit: 284ebe63db9256dc83f745f861809859abec995e
```
```bash
# Initialize the node
saod init $MONIKER_SAO --chain-id $CHAIN_ID_SAO
```
```bash
# Download Genesis
wget -O $HOME/.sao/config/genesis.json "https://anode.team/SAO/test/genesis.json"

# Check Genesis
sha256sum $HOME/.sao/config/genesis.json
# fbd400351e29ca405906937ee343f0be099903d506d7ae06249c23d8922d6794
```
```bash
# Set the ports

# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${PORT_SAO}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${PORT_SAO}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${PORT_SAO}061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${PORT_SAO}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${PORT_SAO}660\"%" $HOME/.sao/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${PORT_SAO}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${PORT_SAO}91\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${PORT_SAO}27\"%" $HOME/.sao/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${PORT_SAO}657\"%" $HOME/.sao/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:${PORT_SAO}656\"/" $HOME/.sao/config/config.toml
```

***Setup config***
```bash
# correct config (so we can no longer use the chain-id flag for every CLI command in client.toml)
saod config chain-id sao-testnet0

# adjust if necessary keyring-backend в client.toml 
saod config keyring-backend os

saod config node tcp://localhost:${PORT_SAO}657

# Set the minimum price for gas
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025sao\"/;" ~/.sao/config/app.toml

# Add seeds/peers в config.toml
peers=""
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.sao/config/config.toml

seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.sao/config/config.toml

# Set up filter for "bad" peers
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.sao/config/config.toml

# Consensus
sed -i -e "s/^timeout_commit *=.*/timeout_commit = \"10s\"/" $HOME/.sao/config/config.toml
sed -i -e "s/^timeout_propose *=.*/timeout_propose = \"10s\"/" $HOME/.sao/config/config.toml
sed -i -e "s/^create_empty_blocks_interval *=.*/create_empty_blocks_interval = \"10s\"/" $HOME/.sao/config/config.toml
sed -i 's/create_empty_blocks = .*/create_empty_blocks = true/g' ~/.sao/config/config.toml
sed -i 's/timeout_broadcast_tx_commit = ".*s"/timeout_broadcast_tx_commit = "10s"/g' ~/.sao/config/config.tom

# Set up pruning
pruning="custom"
pruning_keep_recent="1000"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.sao/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.sao/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.sao/config/app.toml
```

***(OPTIONAL) Turn off indexing in config.toml***
```bash
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.sao/config/config.toml
```

***Cosmovisor***
```bash
# Install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest
```
```bash
# Create directories
mkdir -p ~/.sao/cosmovisor
mkdir -p ~/.sao/cosmovisor/genesis
mkdir -p ~/.sao/cosmovisor/genesis/bin
mkdir -p ~/.sao/cosmovisor/upgrades
```
```bash
# Copy the binary file to the cosmovisor folder
cp `which saod` ~/.sao/cosmovisor/genesis/bin/saod
```
```bash
# Create service file (One command)
sudo tee /etc/systemd/system/saod.service > /dev/null <<EOF
[Unit]
Description=saod daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start --x-crisis-skip-assert-invariants
Restart=always
RestartSec=3
LimitNOFILE=infinity

Environment="DAEMON_NAME=saod"
Environment="DAEMON_HOME=${HOME}/.sao"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"

[Install]
WantedBy=multi-user.target
EOF
```
```bash
# Start the node
systemctl daemon-reload
systemctl enable saod
systemctl restart saod && journalctl -u saod -f -o cat

# Escape from logs ctrl+c
```
```bash
# Check the logs again
journalctl -u saod -f -o cat

# Escape from logs ctrl+c
```

Now all ok, Check status
```bash
saod status 2>&1 | jq "{catching_up: .SyncInfo.catching_up}"
"catching_up": false means that the node is synchronized, we are waiting for complete synchronization
```

***Wallets***
```bash
# Create wallet
saod keys add wallet
```

Create a password for the wallet and write it down so you don't forget it. The wallet has been created. In the last line there will be a phrase that must be written down
```bash
# If the wallet was already there, restore it
saod keys add wallet --recover
# Insert the seed phrase from your wallet
# If everything is correct, you will see your wallet data
```

Go to the # faucet branch and request tokens
```bash
# Save the wallet address
# Replace <your_address> with your wallet address
WALLET_SAO=<your_address>
echo "export WALLET_SAO="${WALLET_SAO}"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
```bash
# Check the ballance
saod q bank balances $WALLET_SAO
```

***Validator***

Do not forget to create a profile on https://keybase.io/ and set a profile photo there that will be imported by key and used for your validators.
```bash
# Change <identity> to your key from keybase
saod tx staking create-validator \
--amount 1000000sao \
--from=$WALLET_SAO \
--commission-rate "0.05" \
--commission-max-rate "0.20" \
--commission-max-change-rate "0.1" \
--min-self-delegation "1" \
--pubkey=$(saod tendermint show-validator) \
--moniker=$MONIKER_SAO \
--chain-id=$CHAIN_ID_SAO \
--fees=5000sao \
--identity="" \
--details="" \
--website="" \
-y
```

Check yourself in the list explorer

Or by command
```bash
saod query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="$MONIKER_SAO")' | jq
```
```bash
# Edit the validator
saod tx staking edit-validator \
  --new-moniker=$MONIKER_SAO \
  --website="" \
  --identity=<identity> \
  --details="" \
  --chain-id=$CHAIN_ID_SAO \
  --fees=1000sao \
  --from=wallet
```
```bash
# Save valoper_address in bash
# Change <your_valoper_address> to the address of the validator, starting with andrvaloper...
VALOPER_SAO=<your_valoper_address>
echo "export VALOPER_SAO="${VALOPER_SAO}"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

!!! Save priv_validator_key.json which located in /root/.sao/config
