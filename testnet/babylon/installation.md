# Installation

_**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/babylon)
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
# Install Go
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/system/go)
```

```bash
# Set the variables

# Come up with the name of your node and replace it instead <your_moniker>
MONIKER=<your_moniker>

echo 'export MONIKER='$MONIKER >> $HOME/.bash_profile
echo "export BABYLON_CHAIN_ID=bbn-test-2" >> $HOME/.bash_profile
echo "export BABYLON_PORT=45" >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```

```bash
# Download binary files
cd $HOME
git clone https://github.com/babylonchain/babylon && cd babylon
git checkout v0.7.2
make install

sudo cp $(which babylond) /usr/local/bin/ && cd $HOME
babylond version --long | grep -e version -e commit
# v0.7.2
```

```bash
# Initialize the node
babylond init $MONIKER --chain-id $BABYLON_CHAIN_ID
```

```bash
# Download Genesis
wget -O $HOME/.babylond/config/genesis.json https://snapshots-testnet.nodejumper.io/babylon-testnet/genesis.json

# Check Genesis
sha256sum $HOME/.babylond/config/genesis.json

# 091bd1c0ec67178faae923eb3b21020d6d4b5844de31bf314d7936168b3aa18c
```

```bash
# Set the ports

# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${BABYLON_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${BABYLON_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${BABYLON_PORT}061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${BABYLON_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${BABYLON_PORT}660\"%" $HOME/.babylond/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${BABYLON_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${BABYLON_PORT}91\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1${BABYLON_PORT}7\"%" $HOME/.babylond/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${BABYLON_PORT}657\"%" $HOME/.babylond/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:${BABYLON_PORT}656\"/" $HOME/.babylond/config/config.toml
```

**Setup config**

```bash
# correct config (so we can no longer use the chain-id flag for every CLI command in client.toml)
babylond config chain-id $BABYLON_CHAIN_ID

# adjust if necessary keyring-backend в client.toml 
babylond config keyring-backend test

# Set the minimum price for gas
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.00001ubbn\"/" ~/.babylond/config/app.toml

# Add seeds/peers в config.toml
peers="30191694cc7836642e7c98f63dc968dfcf453146@babylon-testnet-peer.itrocket.net:39656"
sed -i -e "s|^persistent_peers *=.*|persistent_peers = \"$peers\"|" $HOME/.babylond/config/config.toml
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.babylond/config/config.toml

seeds="8da45f9ff83b4f8dd45bbcb4f850999637fbfe3b@seed0.testnet.babylonchain.io:26656,4b1f8a774220ba1073a4e9f4881de218b8a49c99@seed1.testnet.babylonchain.io:26656,cf36fd32c32e0bb89682e8b8e82c03049a0f0121@babylon-testnet-seed.itrocket.net:32656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.babylond/config/config.toml

# Set up filter for "bad" peers
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.babylond/config/config.toml

# Set up pruning
pruning="custom"
pruning_keep_recent="1000"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.babylond/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.babylond/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.babylond/config/app.toml

sed -i -e "s/^key-name *=.*/key-name = \"wallet\"/" ~/.babylond/config/app.toml
sed -i -e "s/^timeout_commit *=.*/timeout_commit = \"10s\"/" ~/.babylond/config/config.toml
```

**(OPTIONAL) Turn off indexing in config.toml**

```bash
indexer="null" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.babylond/config/config.toml
```

```bash
# Create service file (One command)
sudo tee /etc/systemd/system/babylond.service > /dev/null <<EOF
[Unit]
Description=Babylond Node
After=network.target
 
[Service]
Type=simple
User=$USER
WorkingDirectory=$HOME/go/bin
ExecStart=/usr/local/bin/babylond start
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
systemctl enable babylond
systemctl restart babylond && journalctl -u babylond -f -o cat

# Escape from logs ctrl+c
```

```bash
# Check the logs again
journalctl -u babylond -f -o cat

# Escape from logs ctrl+c
```

**Now all ok, check the status**

```bash
# Check status
curl localhost:${BABYLON_PORT}657/status

# "catching_up": false means that the node is synchronized, we are waiting for complete synchronization
```

**Wallet**

```bash
# Create wallet
babylond keys add wallet
```

The wallet has been created. In the last line there will be a phrase that must be written down

```bash
# If the wallet was already there, restore it
babylond keys add wallet --recover
# Insert the seed phrase from your wallet
# If everything is correct, you will see your wallet data
```

Go to the [#faucet](https://discord.com/channels/1046686458070700112/1075371070493831259) branch and request tokens

```bash
# Save the wallet address
BABYLON_ADDRESS=$(babylond keys show wallet -a)
BABYLON_VALOPER=$(babylond keys show wallet --bech val -a)
echo "export BABYLON_ADDRESS="${BABYLON_ADDRESS} >> $HOME/.bash_profile
echo "export BABYLON_VALOPER="${BABYLON_VALOPER} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
babylond create-bls-key $BABYLON_ADDRESS
systemctl restart babylond && journalctl -u babylond -f -o cat
```

```bash
# Check the ballance
babylond query bank balances $BABYLON_ADDRESS
```

**Validator**

Do not forget to create a profile on [https://keybase.io/](https://keybase.io/) and set a profile photo there that will be imported by key and used for your validators.

```bash
# Change <identity> to your key from keybase
babylond tx staking create-validator \
--amount 10000000ubbn \
--from=wallet \
--commission-rate "0.1" \
--commission-max-rate "0.20" \
--commission-max-change-rate "0.1" \
--min-self-delegation "1" \
--pubkey=$(babylond tendermint show-validator) \
--moniker=$MONIKER \
--chain-id=$BABYLON_CHAIN_ID \
--identity=<identity> \
--details="" \
--website="" \
--gas="auto" \
--gas-adjustment=1.2 \
--gas-prices="0.0025ubbn" \
-y
```

Check yourself in the list [explorer](https://babylon.explorers.guru/validators)

Or by command

```bash
babylond query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="$MONIKER")' | jq
```

```bash
# Edit the validator
babylond tx staking edit-validator \
  --new-moniker=$MONIKER \
  --website="" \
  --identity=<identity> \
  --details="" \
  --chain-id=$BABYLON_CHAIN_ID \
  --gas="auto" \
  --gas-adjustment=1.2 \
  --gas-prices="0.0025ubbn" \
  --from=wallet \
  -y
```

!!! Save priv\_validator\_key.json which is located in /root/.babylond/config
