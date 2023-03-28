# Installation

**Node**

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
ver="1.20.2" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version

# go version go1.20.2 linux/amd64
```

```bash
# Set the variables

# Come up with the name of your node and replace it instead <your_moniker>
REALIO_NODE_NAME=<your_node_name>

echo 'export REALIO_NODE_NAME='$REALIO_NODE_NAME>> $HOME/.bash_profile
echo "export REALIO_CHAIN_ID=realionetwork_1110-1" >> $HOME/.bash_profile
echo "export REALIO_PORT=12" >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```

```bash
# Download binary files
cd $HOME
git clone https://github.com/realiotech/realio-network.git && cd realio-network
git checkout v0.8.0-rc2
make install

realio-networkd version

# v0.8.0-rc2
```

```bash
# Initialize the node
realio-networkd init $REALIO_NODE_NAME --chain-id $REALIO_CHAIN_ID
```

```bash
# Download Genesis
curl https://raw.githubusercontent.com/realiotech/testnets/master/$REALIO_CHAIN_ID/genesis.json > $HOME/.realio-network/config/genesis.json

# Check Genesis
sha256sum $HOME/.realio-network/config/genesis.json 
# 695c10ad99740908d6a1efb2260b51eeeb1c496ac3e515fcf18933b51633d2e8
```

```bash
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

```bash
Setup config
# correct config (so we can no longer use the chain-id flag for every CLI command in client.toml)
realio-networkd config chain-id $REALIO_CHAIN_ID

# adjust if necessary keyring-backend в client.toml 
realio-networkd config keyring-backend test

realio-networkd config node tcp://localhost:${REALIO_PORT}657

# Set the minimum price for gas
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0ario\"/" $HOME/.realio-network/config/app.toml

# Add seeds/peers в config.toml
PEERS="ec2dbd6e5d25501c50fb8585b5678a7460ef11da@144.126.196.99:26656,5bd91f6e7e3bcaaddead32fd37d67458723fec73@159.223.132.183:26656"
sed -i -e "s|^persistent_peers *=.*|persistent_peers = \"$peers\"|" $HOME/.realio-network/config/config.toml

seeds=""
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
```bash
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.realio-network/config/config.toml
```

```bash
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

```bash
# Start the node
sudo systemctl daemon-reload
sudo systemctl enable realio-networkd
sudo systemctl start realio-networkd

# Escape from logs ctrl+c
```

```bash
# Check the logs again
sudo journalctl -u realio-networkd -f -o cat

# Escape from logs ctrl+c
```

```bash
# Check status
realio-networkd status 2>&1 | jq "{catching_up: .SyncInfo.catching_up}"

"catching_up": false means that the node is synchronized, we are waiting for complete synchronization
```

**Wallets**

```bash
# Create wallet
realio-networkd keys add wallet
# The wallet has been created. In the last line there will be a phrase that must be written down
```

```bash
# If the wallet was already there, restore it
realio-networkd keys add wallet --recover
# Insert the seed phrase from your wallet
# If everything is correct, you will see your wallet data
```

**Send your address to the chanel #[testnet-tokens](https://discord.com/channels/1016319560581914747/1047873677020123186)**

```bash
# Save the wallet address
# Replace <your_address> with your wallet address
REALIO_ADDRESS=<your_address>
echo "export REALIO_ADDRESS=$REALIO_ADDRESS" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
# Check the ballance
realio-networkd q bank balances $REALIO_ADDRESS
```

**Validator**
Do not forget to create a profile on https://keybase.io/ and set a profile photo there that will be imported by key and used for your validators.

```bash
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

Check yourself in the list explorer

Or by command

```bash
realio-networkd query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="$GITOPIA_NODE_NAME")' | jq
```

```bash
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

```bash
# Save valoper_address in bash
# Change <your_valoper_address> to the address of the validator, starting with nibivaloper...
REALIO_VALOPER=<your_valoper_address>
echo "export REALIO_VALOPER=$REALIO_VALOPER" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
  
!!! Save priv_validator_key.json which is located in /root/.gitopiad/config


