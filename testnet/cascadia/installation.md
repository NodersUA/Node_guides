# Installation

_**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/cascadia)
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
if [ "$(go version)" != "go version go1.20.2 linux/amd64" ]; then \
ver="1.21.3" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile ; \
fi

go version

# go version go1.21.3 linux/amd64
```

```bash
# Set the variables

# Come up with the name of your node and replace it instead <your_moniker>
MONIKER=<your_moniker>

echo 'export MONIKER='$MONIKER >> $HOME/.bash_profile
echo "export CASCADIA_CHAIN_ID=cascadia_11029-1" >> $HOME/.bash_profile
echo "export CASCADIA_PORT=18" >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```

```bash
# Download binary files
cd $HOME
git clone https://github.com/cascadiafoundation/cascadia && cd cascadia
git checkout v0.2.0
make install

sudo cp $(which cascadiad) /usr/local/bin/ && cd $HOME
cascadiad version --long | grep -e version -e commit
# v0.2.0
```

```bash
# Initialize the node
cascadiad init $MONIKER --chain-id $CASCADIA_CHAIN_ID
```

```bash
# Download Genesis
wget -O $HOME/.cascadiad/config/genesis.json https://raw.githubusercontent.com/CascadiaFoundation/chain-configuration/master/testnet/genesis.json

# Check Genesis
sha256sum $HOME/.cascadiad/config/genesis.json

# 9e7a698f532042cb966c4cda3ed6eb0aab5ce4882ebfb8d334b27dd84fa497bd
```

```bash
# Set the ports

# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CASCADIA_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CASCADIA_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CASCADIA_PORT}061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CASCADIA_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CASCADIA_PORT}660\"%" $HOME/.cascadiad/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CASCADIA_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CASCADIA_PORT}91\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1${CASCADIA_PORT}7\"%" $HOME/.cascadiad/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${CASCADIA_PORT}657\"%" $HOME/.cascadiad/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:${CASCADIA_PORT}656\"/" $HOME/.cascadiad/config/config.toml
```

**Setup config**

```bash
# correct config (so we can no longer use the chain-id flag for every CLI command in client.toml)
cascadiad config chain-id $CASCADIA_CHAIN_ID

# adjust if necessary keyring-backend в client.toml 
cascadiad config keyring-backend test

#cascadiad config node tcp://localhost:${CASCADIA_PORT}657

# Set the minimum price for gas
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025aCC\"/" ~/.cascadiad/config/app.toml

# Add seeds/peers в config.toml
peers=$(curl  https://raw.githubusercontent.com/CascadiaFoundation/chain-configuration/master/testnet/persistent_peers.txt)
sed -i -e "s|^persistent_peers *=.*|persistent_peers = \"$peers\"|" $HOME/.cascadiad/config/config.toml
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.cascadiad/config/config.toml

seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.cascadiad/config/config.toml

# Set up filter for "bad" peers
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.cascadiad/config/config.toml

# Set up pruning
pruning="custom"
pruning_keep_recent="1000"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.cascadiad/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.cascadiad/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.cascadiad/config/app.toml
```

**(OPTIONAL) Turn off indexing in config.toml**

```bash
indexer="null" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.cascadiad/config/config.toml
```

```bash
# Create service file (One command)
sudo tee /etc/systemd/system/cascadiad.service > /dev/null <<EOF
[Unit]
Description=Cascadia Node
After=network.target
 
[Service]
Type=simple
User=$USER
WorkingDirectory=$HOME/go/bin
ExecStart=/usr/local/bin/cascadiad start --trace --log_level info --json-rpc.api eth,txpool,personal,net,debug,web3 --api.enable --chain-id $CASCADIA_CHAIN_ID
Restart=on-failure
StartLimitInterval=0
RestartSec=3
LimitNOFILE=65535
LimitMEMLOCK=209715200
 
[Install]
WantedBy=multi-user.target
EOF
```

```bash
# Start the node
systemctl daemon-reload
systemctl enable cascadiad
systemctl restart cascadiad && journalctl -u cascadiad -f -o cat

# Escape from logs ctrl+c
```

```bash
# Check the logs again
journalctl -u cascadiad -f -o cat

# Escape from logs ctrl+c
```

**Now all ok, check the status**

```bash
# Check status
curl localhost:${CASCADIA_PORT}657/status

# "catching_up": false means that the node is synchronized, we are waiting for complete synchronization
```

**Wallet**

```bash
# Create wallet
cascadiad keys add wallet
```

The wallet has been created. In the last line there will be a phrase that must be written down

```bash
# If the wallet was already there, restore it
cascadiad keys add wallet --recover
# Insert the seed phrase from your wallet
# If everything is correct, you will see your wallet data
```

Go to the [#faucet](https://discord.com/channels/932501599035732008/1093030023012814948) branch and request tokens

```bash
# Save the wallet address
CASCADIA_ADDRESS=$(cascadiad keys show wallet -a)
CASCADIA_VALOPER=$(cascadiad keys show wallet --bech val -a)
echo "export CASCADIA_ADDRESS="${CASCADIA_ADDRESS} >> $HOME/.bash_profile
echo "export CASCADIA_VALOPER="${CASCADIA_VALOPER} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
# Check the ballance
cascadiad query bank balances $CASCADIA_ADDRESS
```

**Validator**

Do not forget to create a profile on [https://keybase.io/](https://keybase.io/) and set a profile photo there that will be imported by key and used for your validators.

```bash
# Change <identity> to your key from keybase
cascadiad tx staking create-validator \
--amount 1000000000000000000aCC \
--from=wallet \
--commission-rate "0.05" \
--commission-max-rate "0.20" \
--commission-max-change-rate "0.1" \
--min-self-delegation "1" \
--pubkey=$(cascadiad tendermint show-validator) \
--moniker=$MONIKER \
--chain-id=$CASCADIA_CHAIN_ID \
--identity=<identity> \
--details="" \
--website="" \
--gas 300000 \
--gas-adjustment=1.2 \
--gas-prices=7aCC \
--broadcast-mode sync \
-y
```

Check yourself in the list [explorer](https://testnet.cascadia.explorers.guru/)

Or by command

```bash
cascadiad query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="$CASCADIA_MONIKER")' | jq
```

```bash
# Edit the validator
cascadiad tx staking edit-validator \
  --new-moniker=$CASCADIA_MONIKER \
  --website="" \
  --identity=<identity> \
  --details="" \
  --chain-id=$CASCADIA_CHAIN_ID \
  --gas 300000 \
  --gas-adjustment=1.4 \
  --gas-prices=7aCC \
  --from=wallet
```

!!! Save priv\_validator\_key.json which is located in /root/.cascadiad/config
