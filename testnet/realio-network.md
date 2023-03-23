---
description: Installation Guide
---

# Realio Network

[WebSite](https://www.realio.fund/) | [Discord](https://discord.gg/7krHawGA) | [Twitter](https://twitter.com/realio\_network) | [Explorer](https://testnet-explorer.realio.network/) | [Docs](https://docs.realio.network/)

### About project <a href="#y32n" id="y32n"></a>

Realio Network is a protocol that provides exclusive access to institutional-level securities.\
Investors seeking institutional-grade opportunities that offer exceptional risk/reward profiles often require tens of millions of dollars and an institutional-grade network to gain access to these top-tier investment vehicles. Realio's technology platform and network provide access, unlocking exclusive opportunities while leveraging next-generation blockchain technology to remove existing investment barriers and reduce or eliminate fees.\
Realio provides SEC-compliant technology to maximize liquidity through peer-to-peer transactions across a network of whitelisted investors and liquidity pools.

### Node <a href="#dnwc" id="dnwc"></a>

```
# Update the repositories
apt update && apt upgrade -y
```

```
# Install developer packages
apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```

```
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

```
# Set the variables

# Come up with the name of your node and replace it instead <your_moniker>
REALIO_NODE_NAME=<your_node_name>

echo 'export REALIO_NODE_NAME='$REALIO_NODE_NAME>> $HOME/.bash_profile
echo "export REALIO_CHAIN_ID=realionetwork_1110-2" >> $HOME/.bash_profile
echo "export REALIO_PORT=12" >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```

```
# Download binary files
cd $HOME
git clone https://github.com/realiotech/realio-network.git && cd realio-network
git checkout tags/v0.7.2
make install

realio-networkd version

# version: 0.7.2
```

```
# Initialize the node
realio-networkd init $REALIO_NODE_NAME --chain-id $REALIO_CHAIN_ID
```

```
# Download Genesis
curl https://raw.githubusercontent.com/realiotech/testnets/master/$REALIO_CHAIN_ID/genesis.json > $HOME/.realio-network/config/genesis.json

# Check Genesis
sha256sum $HOME/.realio-network/config/genesis.json 
# a2f8fae48eb019720ef78524d683a9ca22884dd4e9ba4f8d5b3ac10db1275183
```

```
# Set the ports

# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${REALIO_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${REALIO_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${REALIO_PORT}061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${REALIO_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${REALIO_PORT}660\"%" $HOME/.realio-network/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${REALIO_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${REALIO_PORT}91\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1${REALIO_PORT}7\"%" $HOME/.realio-network/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${REALIO_PORT}657\"%" $HOME/.realio-network/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:${REALIO_PORT}656\"/" $HOME/.realio-network/config/config.toml
```

**Setup config**

```
# correct config (so we can no longer use the chain-id flag for every CLI command in client.toml)
realio-networkd config chain-id $REALIO_CHAIN_ID

# adjust if necessary keyring-backend в client.toml 
realio-networkd config keyring-backend test

realio-networkd config node tcp://localhost:${REALIO_PORT}657

# Set the minimum price for gas
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0ario\"/" $HOME/.realio-network/config/app.toml

# Add seeds/peers в config.toml
peers=""
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.realio-network/config/config.toml

seeds="aa194e9f9add331ee8ba15d2c3d8860c5a50713f@143.110.230.177:26656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.realio-network/config/config.toml

# Set up filter for "bad" peers
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.realio-network/config/config.toml

#
sed -i -e "s/^timeout_commit *=.*/timeout_commit = \"60s\"/" $HOME/.realio-network/config/config.toml
sed -i -e "s/^timeout_propose *=.*/timeout_propose = \"60s\"/" $HOME/.realio-network/config/config.toml
sed -i -e "s/^create_empty_blocks_interval *=.*/create_empty_blocks_interval = \"60s\"/" $HOME/.realio-network/config/config.toml
sed -i 's/create_empty_blocks = .*/create_empty_blocks = true/g' ~/.realio-network/config/config.toml
sed -i 's/timeout_broadcast_tx_commit = ".*s"/timeout_broadcast_tx_commit = "601s"/g' ~/.realio-network/config/config.toml

# Set up pruning
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.realio-network/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.realio-network/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.realio-network/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.realio-network/config/app.toml
```

**(OPTIONAL) Turn off indexing in config.toml**

```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.realio-network/config/config.toml
```

**Sanpshot**

```
snapshot_interval=1000 && \
sed -i.bak -e "s/^snapshot-interval *=.*/snapshot-interval = \"$snapshot_interval\"/" ~/.realio-network/config/app.toml
```

**State Sync**

```
sudo systemctl stop realio-networkd
cp $HOME/.realio-network/data/priv_validator_state.json $HOME/.realio-network/priv_validator_state.json.backup
realio-networkd tendermint unsafe-reset-all --home $HOME/.realio-network

STATE_SYNC_RPC=http://rpc.realio.ppnv.space:16657
STATE_SYNC_PEER=d0de51b7de393935cdc29dda114431964be93090@rpc.realio.ppnv.space:16656

LATEST_HEIGHT=$(curl -s $STATE_SYNC_RPC/block | jq -r .result.block.header.height)
SYNC_BLOCK_HEIGHT=$(($LATEST_HEIGHT - 1000))
SYNC_BLOCK_HASH=$(curl -s "$STATE_SYNC_RPC/block?height=$SYNC_BLOCK_HEIGHT" | jq -r .result.block_id.hash)

sed -i.bak -e "s|^enable *=.*|enable = true|" $HOME/.realio-network/config/config.toml

sed -i.bak -e "s|^rpc_servers *=.*|rpc_servers = \"$STATE_SYNC_RPC,$STATE_SYNC_RPC\"|" $HOME/.realio-network/config/config.toml

sed -i.bak -e "s|^trust_height *=.*|trust_height = $SYNC_BLOCK_HEIGHT|" $HOME/.realio-network/config/config.toml
sed -i.bak -e "s|^trust_hash *=.*|trust_hash = \"$SYNC_BLOCK_HASH\"|" $HOME/.realio-network/config/config.toml
sed -i.bak -e "s|^persistent_peers *=.*|persistent_peers = \"$STATE_SYNC_PEER\"|" $HOME/.realio-network/config/config.toml

mv $HOME/.realio-network/priv_validator_state.json.backup $HOME/.realio-network/data/priv_validator_state.json
sudo systemctl restart realio-networkd && journalctl -u realio-networkd -f --no-hostname -o cat
```

```
# Create service file (One command)
sudo tee /etc/systemd/system/realio-networkd.service > /dev/null <<EOF
[Unit]
Description=realio-networkd
After=network-online.target

[Service]
User=$USER
ExecStart=$(which realio-networkd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

```
# Start the node
sudo systemctl daemon-reload
sudo systemctl enable realio-networkd
sudo systemctl start realio-networkd

# Escape from logs ctrl+c
```

```
# Check the logs again
sudo journalctl -u realio-networkd -f -o cat

# Escape from logs ctrl+c
```

```
# Check status
realio-networkd status 2>&1 | jq "{catching_up: .SyncInfo.catching_up}"

"catching_up": false means that the node is synchronized, we are waiting for complete synchronization
```

### Wallets <a href="#2osy" id="2osy"></a>

```
# Create wallet
realio-networkd keys add wallet
```

Create a password for the wallet and write it down so you don't forget it. The wallet has been created. In the last line there will be a phrase that must be written down

```
# If the wallet was already there, restore it
realio-networkd keys add wallet --recover
# Insert the seed phrase from your wallet
# If everything is correct, you will see your wallet data
```

Go to the [# ](https://discord.com/channels/947911971515293759/984840062871175219)[faucet](https://discord.com/channels/1016319560581914747/1047873677020123186) branch and request tokens

```
# Save the wallet address
# Replace <your_address> with your wallet address
REALIO_ADDRESS=<your_address>
echo "export REALIO_ADDRESS=$REALIO_ADDRESS" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```
# Check the ballance
realio-networkd q bank balances $REALIO_ADDRESS
```

### Validator <a href="#zris" id="zris"></a>

Do not forget to create a profile on [https://keybase.io/](https://keybase.io/) and set a profile photo there that will be imported by key and used for your validators.

```
# Change <identity> to your key from keybase
realio-networkd tx staking create-validator \
--amount 1000000000000000000ario \
--from=$REALIO_WALLET_NAME \
--commission-rate "0.05" \
--commission-max-rate "0.20" \
--commission-max-change-rate "0.1" \
--min-self-delegation "1" \
--pubkey=$(realio-networkd tendermint show-validator) \
--moniker=$REALIO_NODE_NAME \
--chain-id=$REALIO_CHAIN_ID \
--fees=5000000000000000ario \
--identity="" \
--details="" \
--website="" \
-y
```

Check yourself in the list [ecsplorer](https://andromeda.explorers.guru/validators)

Or by command

```
realio-networkd query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="$GITOPIA_NODE_NAME")' | jq
```

```
# Edit the validator
realio-networkd tx staking edit-validator \
  --new-moniker=$REALIO_NODE_NAME \
  --website="" \
  --identity=<identity> \
  --details="" \
  --chain-id=$REALIO_CHAIN_ID \
  --fees=5000000000000000ario \
  --from=$REALIO_WALLET_NAME
  
```

```
# Save valoper_address in bash
# Change <your_valoper_address> to the address of the validator, starting with nibivaloper...
REALIO_VALOPER=<your_valoper_address>
echo "export REALIO_VALOPER=$REALIO_VALOPER" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

!!! Save _priv\_validator\_key.json_ which is located in /root/.gitopiad/config

### Information <a href="#amhh" id="amhh"></a>

```
# Check the blocks
realio-networkd status 2>&1 | jq ."SyncInfo"."latest_block_height"

# Check logs
journalctl -u realio-networkd -f -o cat

# Check status
realio-networkd status 2>&1 | jq .SyncInfo

# Check balance
realio-networkd q bank balances $REALIO_WALLET_NAME

# Check pubkey of validator
realio-networkd tendermint show-validator

# Check validator
realio-networkd q staking validator $REALIO_VALOPER
realio-networkd q staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker="$REALIO_VALOPER")' | jq

# Check information of TX_HASH
realio-networkd q tx <TX_HASH>

# Check how many blocks were passed by the validator and from which block the asset
realio-networkd q slashing signing-info $(realio-networkd tendermint show-validator)
```

```
Transactions

# Collect rewards from all validators delegated to them (without commission)
realio-networkd tx distribution withdraw-all-rewards --from wallet --fees 5000000000000000ario --gas=300000 -y

# Collect rewards from a separate validator or rewards + commission from your own validator
realio-networkd tx distribution withdraw-rewards $REALIO_VALOPER --from wallet --fees 5000000000000000ario --gas=300000 --commission -y

# Delegate yourself (this is how 1 coin is sent)
realio-networkd tx staking delegate $REALIO_VALOPER 1000000000000000000ario --from wallet --fees 5000000000000000ario --gas=300000 -y

# Redelegate to other validator
realio-networkd tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000000000000000ario --from wallet --fees 5000000000000000ario --gas=300000 -y

# unbond 
realio-networkd tx staking unbond $REALIO_VALOPER 1000000000000000000ario --from wallet --fees 5000000000000000ario --gas=300000 -y

# Send tokens to other adress
realio-networkd tx bank send wallet <address> 1000000000000000000ario --fees 5000000000000000ario --gas=300000 -y

# Escape from jail
realio-networkd tx slashing unjail --from wallet --fees 5000000000000000ario --gas=300000 -y
```

! If the transactions are not sent with the error account sequence mismatch, expected 18, got 17: incorrect account sequence, then add the flag -s 18 to the command (replace the number with the one that is waiting for the sequence)

**Work with wallets**

```
# Chech the wallets list
realio-networkd keys list

# Delete wallet
realio-networkd keys delete <name_wallet>
```

**Delete Node**

```
sudo systemctl stop realio-networkd && \
sudo systemctl disable realio-networkd && \
rm /etc/systemd/system/realio-networkd.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf realio-networkd && \
rm -rf .realio-networkd && \
rm -rf $(which realio-networkd)
```

Governance

```
# List proposals
realio-networkd q gov proposals

# Check voting result
realio-networkd q gov proposals --voter $REALIO_VALOPER

# Vote 
realio-networkd tx gov vote 1 yes --from $REALIO_WALLET_NAME
```

**Peers and RPC**

```
FOLDER=.realio-networkd

# Check your peer
PORTR=$(grep -A 3 "\[p2p\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+") && \
echo $(realio-networkd tendermint show-node-id)@$(curl ifconfig.me)$PORTR

# Check port of RPC
echo -e "\033[0;32m$(grep -A 3 "\[rpc\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+")\033[0m"

# Check peers quantity
PORT=
curl -s http://localhost:$REALIO_PORT/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr | split(":")[2])"' | wc -l
```

### We are a small group of enthusiasts who participate in testnet and validate projects on the mainet, invest in promising projects. <a href="#iiry" id="iiry"></a>

#### Discord - [https://discord.gg/cvhkpvsz](https://discord.gg/cvhkpvsz) <a href="#tptv" id="tptv"></a>

#### Telegram - <a href="#rjba" id="rjba"></a>
