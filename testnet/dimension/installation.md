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
MONIKER_DYMENSION=<your_moniker>

echo 'export MONIKER_DYMENSION='$MONIKER_DYMENSION >> $HOME/.bash_profile
echo "export CHAIN_ID_DYMENSION=35-C" >> $HOME/.bash_profile
echo "export PORT_DYMENSION=35" >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```
```bash
# Download binary files
cd $HOME 
git clone https://github.com/dymensionxyz/dymension
cd dymension
git fetch --all 
git checkout v0.2.0-beta
make install
sudo cp $HOME/go/bin/dymd /usr/local/bin/dymd
dymd version --long | grep -e version -e commit
# v0.2.0-beta
```
```bash
# Initialize the node
dymd init $MONIKER_DYMENSION --chain-id $CHAIN_ID_DYMENSION
```
```bash
# Download Genesis
wget https://raw.githubusercontent.com/obajay/nodes-Guides/main/Dymension/genesis.json -O $HOME/.dymension/config/genesis.json

# Check Genesis
sha256sum $HOME/.dymension/config/genesis.json
# cf20e3b15d089ceeaaa9bb2abcd48a50f98e9f2274f4320aeae534d6972c4ee2
```
```bash
# Set the ports

# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${PORT_DYMENSION}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${PORT_DYMENSION}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${PORT_DYMENSION}061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${PORT_DYMENSION}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${PORT_DYMENSION}660\"%" $HOME/.dymension/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${PORT_DYMENSION}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${PORT_DYMENSION}91\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${PORT_DYMENSION}27\"%" $HOME/.dymension/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${PORT_DYMENSION}657\"%" $HOME/.dymension/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:${PORT_DYMENSION}656\"/" $HOME/.dymension/config/config.toml
```

***Setup config***
```bash
# correct config (so we can no longer use the chain-id flag for every CLI command in client.toml)
dymd config chain-id 35-C

# adjust if necessary keyring-backend в client.toml 
dymd config keyring-backend os

dymd config node tcp://localhost:${PORT_DYMENSION}657

# Set the minimum price for gas
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025udym\"/;" ~/.dymension/config/app.toml

# Add seeds/peers в config.toml
peers="ebc272824924ea1a27ea3183dd0b9ba713494f83@dymension-testnet-peer.autostake.net:27086,9111fd409e5521470b9b33a46009f5e53c646a0d@178.62.81.245:45656,f8a0d7c7db90c53a989e2341746b88433f47f980@209.182.238.30:30657,1bffcd1690806b5796415ff72f02157ce048bcdd@144.76.67.53:2580,c17a4bcba59a0cbb10b91cd2cee0940c610d26ee@95.217.144.107:20556,e6ea3444ac85302c336000ac036f4d86b97b3d3e@38.146.3.199:20556,b473a649e58b49bc62b557e94d35a2c8c0ee9375@95.214.53.46:36656,db0264c412618949ce3a63cb07328d027e433372@146.19.24.101:26646,281190aa44ca82fb47afe60ba1a8902bae469b2a@dymension.peers.stavr.tech:17806,d8b1bcfc123e63b24d0ebf86ab674a0fc5cb3b06@51.159.97.212:26656,55f233c7c4bea21a47d266921ca5fce657f3adf7@168.119.240.200:26656,139340424dddf85e54e0a54179d06875013e1e39@65.109.87.88:24656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.dymension/config/config.toml

seeds="f97a75fb69d3a5fe893dca7c8d238ccc0bd66a8f@dymension-testnet.seed.brocha.in:30584"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.dymension/config/config.toml

# Set up filter for "bad" peers
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.dymension/config/config.toml

#
sed -i -e "s/^timeout_commit *=.*/timeout_commit = \"10s\"/" $HOME/.dymension/config/config.toml
sed -i -e "s/^timeout_propose *=.*/timeout_propose = \"10s\"/" $HOME/.dymension/config/config.toml
sed -i -e "s/^create_empty_blocks_interval *=.*/create_empty_blocks_interval = \"10s\"/" $HOME/.dymension/config/config.toml
sed -i 's/create_empty_blocks = .*/create_empty_blocks = true/g' ~/.dymension/config/config.toml
sed -i 's/timeout_broadcast_tx_commit = ".*s"/timeout_broadcast_tx_commit = "10s"/g' ~/.dymension/config/config.tom

# Set up pruning
pruning="custom"
pruning_keep_recent="1000"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.dymension/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.dymension/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.dymension/config/app.toml
```

***(OPTIONAL) Turn off indexing in config.toml***
```bash
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.dymension/config/config.toml
```

***Cosmovisor***
```bash
# Install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest
```
```bash
# Create directories
mkdir -p ~/.dymension/cosmovisor
mkdir -p ~/.dymension/cosmovisor/genesis
mkdir -p ~/.dymension/cosmovisor/genesis/bin
mkdir -p ~/.dymension/cosmovisor/upgrades
```
```bash
# Copy the binary file to the cosmovisor folder
cp `which dymd` ~/.dymension/cosmovisor/genesis/bin/dymd
```
```bash
# Create service file (One command)
sudo tee /etc/systemd/system/dymd.service > /dev/null <<EOF
[Unit]
Description=dymd daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start --x-crisis-skip-assert-invariants
Restart=always
RestartSec=3
LimitNOFILE=infinity

Environment="DAEMON_NAME=dymd"
Environment="DAEMON_HOME=${HOME}/.dymension"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"

[Install]
WantedBy=multi-user.target
EOF
```
```bash
# Start the node
systemctl daemon-reload
systemctl enable dymd
systemctl restart dymd && journalctl -u dymd -f -o cat

# Escape from logs ctrl+c
```
```bash
# Check the logs again
journalctl -u dymd -f -o cat

# Escape from logs ctrl+c
```

Now all ok, Check status
```bash
dymd status 2>&1 | jq "{catching_up: .SyncInfo.catching_up}"
"catching_up": false means that the node is synchronized, we are waiting for complete synchronization
```

***Wallets***
```bash
# Create wallet
dymd keys add wallet
```

Create a password for the wallet and write it down so you don't forget it. The wallet has been created. In the last line there will be a phrase that must be written down
```bash
# If the wallet was already there, restore it
dymd keys add wallet --recover
# Insert the seed phrase from your wallet
# If everything is correct, you will see your wallet data
```
Go to the # faucet branch and request tokens
```bash
# Save the wallet address
# Replace <your_address> with your wallet address
WALLET_DYMENSION=<your_address>
echo "export WALLET_DYMENSION="${WALLET_DYMENSION}"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
```bash
# Check the ballance
dymd q bank balances $WALLET_DYMENSION
```

***Validator***

Do not forget to create a profile on https://keybase.io/ and set a profile photo there that will be imported by key and used for your validators.
```bash
# Change <identity> to your key from keybase
dymd tx staking create-validator \
--amount 1000000udym \
--from=$WALLET_DYMENSION \
--commission-rate "0.15" \
--commission-max-rate "0.20" \
--commission-max-change-rate "0.1" \
--min-self-delegation "1" \
--pubkey=$(dymd tendermint show-validator) \
--moniker=$MONIKER_DYMENSION \
--chain-id=$CHAIN_ID_DYMENSION \
--fees=5000udym \
--identity="" \
--details="" \
--website="" \
-y
```

Check yourself in the list explorer

Or by command
```bash
dymd query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="$MONIKER_ANDROMEDA")' | jq
```
```bash
# Edit the validator
dymd tx staking edit-validator \
  --new-moniker=$MONIKER_DYMENSION \
  --website="" \
  --identity=<identity> \
  --details="" \
  --chain-id=$CHAIN_ID_DYMENSION \
  --fees=1000uandr \
  --from=wallet
```
```bash
# Save valoper_address in bash
# Change <your_valoper_address> to the address of the validator, starting with dymdvaloper...
VALOPER_DYMENSION=<your_valoper_address>
echo "export VALOPER_DYMENSION="${VALOPER_DYMENSION}"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
!!! Save priv_validator_key.json which located in /root/.dymension/config

