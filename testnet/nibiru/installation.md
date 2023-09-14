# Installation

_**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/nibiru/nibiru)
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
fi \
go version

# go version go1.20.1 linux/amd64
```

```bash
# Set the variables

# Come up with the name of your node and replace it instead <your_moniker>
NIBIRU_MONIKER=<your_moniker>

echo 'export NIBIRU_MONIKER='$NIBIRU_MONIKER >> $HOME/.bash_profile
echo "export NIBIRU_CHAIN_ID=nibiru-itn-2" >> $HOME/.bash_profile
echo "export NIBIRU_PORT=11" >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```

```bash
# Download binary files
cd $HOME
git clone https://github.com/NibiruChain/nibiru
cd nibiru
git checkout v0.21.9
make install

sudo cp $(which nibid) /usr/local/bin/ && cd $HOME
nibid version --long | grep -e version -e commit
# v0.21.9
```

```bash
# Initialize the node
nibid init $NIBIRU_MONIKER --chain-id $NIBIRU_CHAIN_ID
```

```bash
# Download Genesis
curl -s https://networks.itn2.nibiru.fi/$NIBIRU_CHAIN_ID/genesis > $HOME/.nibid/config/genesis.json

# Check Genesis
shasum -a 256 $HOME/.nibid/config/genesis.json

# a4f2574b7fc308fcb3010e6597e46cbc88c30ad4d094a35293ce4ac31ce342ee
```

```bash
# Set the ports

# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${NIBIRU_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${NIBIRU_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${NIBIRU_PORT}061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${NIBIRU_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${NIBIRU_PORT}660\"%" $HOME/.nibid/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${NIBIRU_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${NIBIRU_PORT}91\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1${NIBIRU_PORT}7\"%" $HOME/.nibid/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${NIBIRU_PORT}657\"%" $HOME/.nibid/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:${NIBIRU_PORT}656\"/" $HOME/.nibid/config/config.toml
```

_**Setup config**_

```bash
# correct config (so we can no longer use the chain-id flag for every CLI command in client.toml)
nibid config chain-id $NIBIRU_CHAIN_ID

# adjust if necessary keyring-backend в client.toml 
nibid config keyring-backend test

nibid config node tcp://localhost:${NIBIRU_PORT}657

# Set the minimum price for gas
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025unibi\"/;" ~/.nibid/config/app.toml

# Add seeds/peers в config.toml
peers=""
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.nibid/config/config.toml

seeds="$(echo $(curl -s https://networks.itn2.nibiru.fi/$NIBIRU_CHAIN_ID/seeds))"
sed -i "s|seeds =.*|seeds = \"$seeds\"|g" $HOME/.nibid/config/config.toml

# Set up filter for "bad peers
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.nibid/config/config.toml

#
sed -i -e "s/^timeout_commit *=.*/timeout_commit = \"60s\"/" $HOME/.nibid/config/config.toml
sed -i -e "s/^timeout_propose *=.*/timeout_propose = \"60s\"/" $HOME/.nibid/config/config.toml
sed -i -e "s/^create_empty_blocks_interval *=.*/create_empty_blocks_interval = \"60s\"/" $HOME/.nibid/config/config.toml
sed -i 's/create_empty_blocks = .*/create_empty_blocks = true/g' ~/.nibid/config/config.toml
sed -i 's/timeout_broadcast_tx_commit = ".*s"/timeout_broadcast_tx_commit = "601s"/g' ~/.nibid/config/config.toml

# Set up pruning
pruning="custom"
pruning_keep_recent="1000"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.nibid/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.nibid/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.nibid/config/app.toml

config_file="$HOME/.nibid/config/config.toml"
sed -i "s|enable =.*|enable = true|g" "$config_file"
sed -i "s|rpc_servers =.*|rpc_servers = \"$(curl -s https://networks.itn2.nibiru.fi/$NIBIRU_CHAIN_ID/rpc_servers)\"|g" "$config_file"
sed -i "s|trust_height =.*|trust_height = \"$(curl -s https://networks.itn2.nibiru.fi/$NIBIRU_CHAIN_ID/trust_height)\"|g" "$config_file"
sed -i "s|trust_hash =.*|trust_hash = \"$(curl -s https://networks.itn2.nibiru.fi/$NIBIRU_CHAIN_ID/trust_hash)\"|g" "$config_file"
```

_**(OPTIONAL) Turn off indexing in config.toml**_

```bash
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.nibid/config/config.toml
```

_**Cosmovisor**_

```bash
# Install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest
```

```bash
# Create directories
mkdir -p ~/.nibid/cosmovisor
mkdir -p ~/.nibid/cosmovisor/genesis
mkdir -p ~/.nibid/cosmovisor/genesis/bin
mkdir -p ~/.nibid/cosmovisor/upgrades
```

```bash
# Copy the binary file to the cosmovisor folder
cp `which nibid` ~/.nibid/cosmovisor/genesis/bin/nibid
```

```bash
# Create service file (One command)
sudo tee /etc/systemd/system/nibid.service > /dev/null <<EOF
[Unit]
Description=nibid daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start --x-crisis-skip-assert-invariants
Restart=always
RestartSec=3
LimitNOFILE=infinity

Environment="DAEMON_NAME=nibid"
Environment="DAEMON_HOME=${HOME}/.nibid"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"

[Install]
WantedBy=multi-user.target
EOF
```

```bash
# Start the node
systemctl daemon-reload
systemctl enable nibid
systemctl restart nibid && journalctl -u nibid -f -o cat

# Escape from logs ctrl+c
```

```bash
# Check the logs again
journalctl -u nibid -f -o cat

# Escape from logs ctrl+c
```

```bash
# Check status
nibid status 2>&1 | jq .SyncInfo

"catching_up": false means that the node is synchronized, we are waiting for complete synchronization
```

_**Wallets**_

```bash
# Create wallet
nibid keys add wallet
```

The wallet has been created. In the last line there will be a phrase that must be written down

```bash
# If the wallet was already there, restore it
nibid keys add wallet --recover
# Insert the seed phrase from your wallet
# If everything is correct, you will see your wallet data
```

Go to the # faucet branch and request tokens

```bash
# Save the wallet and valoper address
NIBIRU_ADDRESS=$(nibid keys show wallet -a)
NIBIRU_VALOPER=$(nibid keys show wallet --bech val -a)
echo "export NIBIRU_ADDRESS="${NIBIRU_ADDRESS} >> $HOME/.bash_profile
echo "export NIBIRU_VALOPER="${NIBIRU_VALOPER} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
# Request tokens
curl -X POST -d '{"address": "'"$NIBIRU_ADDRESS"'", "coins": ["110000000unibi","100000000unusd","100000000uusdt"]}' "https://faucet.itn-2.nibiru.fi/"
```

```bash
# Check the ballance
nibid q bank balances $NIBIRU_ADDRESS
```

_**Validator**_

Do not forget to create a profile on https://keybase.io/ and set a profile photo there that will be imported by key and used for your validators.

```bash
# Change <identity> to your key from keybase
nibid tx staking create-validator \
--amount 1000000unibi \
--from=wallet \
--commission-rate "0.05" \
--commission-max-rate "0.20" \
--commission-max-change-rate "0.1" \
--min-self-delegation "1" \
--pubkey=$(nibid tendermint show-validator) \
--moniker=$NIBIRU_MONIKER \
--chain-id=$NIBIRU_CHAIN_ID \
--fees=5000unibi \
--identity=<identity> \
--details="" \
--website="" \
-y
```

Check yourself in the list [explorer](https://nibiru.explorers.guru/validators)

Or by command

```bash
nibid query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="$NIBIRU_VALOPER")' | jq
```

```bash
# Edit the validator
nibid tx staking edit-validator \
  --new-moniker=$NIBIRU_MONIKER \
  --website="" \
  --identity=<identity> \
  --details="" \
  --chain-id=$NIBIRU_CHAIN_ID \
  --fees=5000unibi \
  --from=wallet
```

**!!! Save priv\_validator\_key.json which is located in /root/.gitopiad/config**

_**Setup price feeder (✔️Oracle)**_

```bash
# Download binary files
cd && curl -s https://get.nibiru.fi/pricefeeder! | bash
```

```bash
# Create separate wallet
nibid keys add feeder_wallet
```

```bash
#Set the variables
export GRPC_ENDPOINT="localhost:${NIBIRU_PORT}90"
export WEBSOCKET_ENDPOINT="ws://localhost:${NIBIRU_PORT}657/websocket"
export EXCHANGE_SYMBOLS_MAP='{ "bitfinex": { "ubtc:uusd": "tBTCUSD", "ueth:uusd": "tETHUSD", "uusdt:uusd": "tUSTUSD" }, "binance": { "ubtc:uusd": "BTCUSD", "ueth:uusd": "ETHUSD", "uusdt:uusd": "USDTUSD", "uusdc:uusd": "USDCUSD", "uatom:uusd": "ATOMUSD", "ubnb:uusd": "BNBUSD", "uavax:uusd": "AVAXUSD", "usol:uusd": "SOLUSD", "uada:uusd": "ADAUSD", "ubtc:unusd": "BTCUSD", "ueth:unusd": "ETHUSD", "uusdt:unusd": "USDTUSD", "uusdc:unusd": "USDCUSD", "uatom:unusd": "ATOMUSD", "ubnb:unusd": "BNBUSD", "uavax:unusd": "AVAXUSD", "usol:unusd": "SOLUSD", "uada:unusd": "ADAUSD" } }'

# Save seed phrase
# Change <your_feeder_mnemonic> to the mnemonic of feeder_wallet
FEEDER_MNEMONIC="<your_feeder_mnemonic>"

# Change <your_feeder_address> to the address of feeder_wallet
FEEDER_ADDRESS="<your_feeder_address>"

echo "export FEEDER_MNEMONIC=$FEEDER_MNEMONIC" >> $HOME/.bash_profile
echo "export FEEDER_ADDRESS=$FEEDER_ADDRESS" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
# Send tokens for fees (around 1000 tokens)
nibid tx bank send wallet $FEEDER_ADDRESS 100000000unibi --fees 7500unibi -y
# Or take from faucet
curl -X POST -d '{"address": "'"$FEEDER_ADDRESS"'", "coins": ["110000000unibi","100000000unusd","100000000uusdt"]}' $NIBIRU_FAUCET_URL
```

```bash
# Check balance
nibid q bank balances $FEEDER_ADDRESS
```

```bash
# Create service file
sudo tee /etc/systemd/system/pricefeeder.service<<EOF
[Unit]
Description=Nibiru Pricefeeder
Requires=network-online.target
After=network-online.target

[Service]
Type=exec
User=$USER
Group=$USER
ExecStart=/usr/local/bin/pricefeeder
Restart=on-failure
ExecReload=/bin/kill -HUP $MAINPID
KillSignal=SIGTERM
PermissionsStartOnly=true
LimitNOFILE=65535
Environment=CHAIN_ID='$NIBIRU_CHAIN_ID'
Environment=GRPC_ENDPOINT='$GRPC_ENDPOINT'
Environment=WEBSOCKET_ENDPOINT='$WEBSOCKET_ENDPOINT'
Environment=EXCHANGE_SYMBOLS_MAP='$EXCHANGE_SYMBOLS_MAP'
Environment=FEEDER_MNEMONIC='$FEEDER_MNEMONIC'
Environment=VALIDATOR_ADDRESS='$NIBIRU_VALOPER'

[Install]
WantedBy=multi-user.target
EOF
```

```bash
nibid tx oracle set-feeder $FEEDER_ADDRESS --from wallet --fees 5000unibi -y
systemctl daemon-reload && \
systemctl enable pricefeeder && \
systemctl restart pricefeeder && journalctl -u pricefeeder -f -o cat

# Exit from logs ctrl+c
```
