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
DEFUND_MONIKER=<your_moniker>

echo 'export DEFUND_MONIKER='$DEFUND_MONIKER >> $HOME/.bash_profile
echo "export DEFUND_CHAIN_ID=orbit-alpha-1" >> $HOME/.bash_profile
echo "export DEFUND_PORT=13" >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```

```bash
# Download binary files
cd $HOME
git clone https://github.com/defund-labs/defund
cd defund
git checkout v0.2.6
make install

defundd version --long | grep -e version -e commit
# 0.2.6
# commit: 8f2ebe3d30efe84e013ec5fcdf21a3b99e786c3d
```

```bash
# Initialize the node
defundd init $DEFUND_MONIKER --chain-id $DEFUND_CHAIN_ID
```

```bash
# Download Genesis
wget -O $HOME/.defund/config/genesis.json "https://raw.githubusercontent.com/defund-labs/testnet/main/orbit-alpha-1/genesis.json"

# Check Genesis
sha256sum $HOME/.defund/config/genesis.json

# 58916f9c7c4c4b381f55b6274bce9b8b8d482bfb15362099814ff7d0c1496658
```

```bash
# Set the ports

# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${DEFUND_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${DEFUND_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${DEFUND_PORT}061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${DEFUND_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${DEFUND_PORT}660\"%" $HOME/.defund/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${DEFUND_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${DEFUND_PORT}91\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1${DEFUND_PORT}7\"%" $HOME/.defund/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${DEFUND_PORT}657\"%" $HOME/.defund/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:${DEFUND_PORT}656\"/" $HOME/.defund/config/config.toml
```

**Setup config**

```bash
# correct config (so we can no longer use the chain-id flag for every CLI command in client.toml)
defundd config chain-id $DEFUND_CHAIN_ID

# adjust if necessary keyring-backend в client.toml 
defundd config keyring-backend test

defundd config node tcp://localhost:${DEFUND_PORT}657

# Set the minimum price for gas
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025ufetf\"/;" ~/.defund/config/app.toml

# Add seeds/peers в config.toml
peers="9f92e47ea6861f75bf8a450a681218baae396f01@94.130.219.37:26656,f03f3a18bae28f2099648b1c8b1eadf3323cf741@162.55.211.136:26656,f8fa20444c3c56a2d3b4fdc57b3fd059f7ae3127@148.251.43.226:56656,70a1f41dea262730e7ab027bcf8bd2616160a9a9@142.132.202.86:17000,
e47e5e7ae537147a23995117ea8b2d4c2a408dcb@172.104.159.69:45656,74e6425e7ec76e6eaef92643b6181c42d5b8a3b8@defund-testnet-seed.itrocket.net:443"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.defund/config/config.toml

seeds="9f92e47ea6861f75bf8a450a681218baae396f01@94.130.219.37:26656,f03f3a18bae28f2099648b1c8b1eadf3323cf741@162.55.211.136:26656,f8fa20444c3c56a2d3b4fdc57b3fd059f7ae3127@148.251.43.226:56656,70a1f41dea262730e7ab027bcf8bd2616160a9a9@142.132.202.86:17000,e47e5e7ae537147a23995117ea8b2d4c2a408dcb@172.104.159.69:45656,74e6425e7ec76e6eaef92643b6181c42d5b8a3b8@defund-testnet-seed.itrocket.net:443"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.defund/config/config.toml

# Set up filter for "bad" peers
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.defund/config/config.toml

#
sed -i -e "s/^timeout_commit *=.*/timeout_commit = \"60s\"/" $HOME/.defund/config/config.toml
sed -i -e "s/^timeout_propose *=.*/timeout_propose = \"60s\"/" $HOME/.defund/config/config.toml
sed -i -e "s/^create_empty_blocks_interval *=.*/create_empty_blocks_interval = \"60s\"/" $HOME/.defund/config/config.toml
sed -i 's/create_empty_blocks = .*/create_empty_blocks = true/g' ~/.defund/config/config.toml
sed -i 's/timeout_broadcast_tx_commit = ".*s"/timeout_broadcast_tx_commit = "601s"/g' ~/.defund/config/config.toml

# Set up pruning
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.defund/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.defund/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.defund/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.defund/config/app.toml
```

**(OPTIONAL) Turn off indexing in config.toml**

```bash
indexer="null" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.defund/config/config.toml
```

**Cosmovisor**

```bash
# Install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest
```

```bash
# Create directories
mkdir -p ~/.defund/cosmovisor
mkdir -p ~/.defund/cosmovisor/genesis
mkdir -p ~/.defund/cosmovisor/genesis/bin
mkdir -p ~/.defund/cosmovisor/upgrades
```

```bash
# Copy the binary file to the cosmovisor folder
cp `which defundd` ~/.defund/cosmovisor/genesis/bin/defundd
```

```bash
# Create service file (One command)
sudo tee /etc/systemd/system/defundd.service > /dev/null <<EOF
[Unit]
Description=defundd daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start --x-crisis-skip-assert-invariants
Restart=always
RestartSec=3
LimitNOFILE=infinity

Environment="DAEMON_NAME=defundd"
Environment="DAEMON_HOME=${HOME}/.defund"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"

[Install]
WantedBy=multi-user.target
EOF
```

```bash
# Start the node
systemctl daemon-reload
systemctl enable defundd
systemctl restart defundd && journalctl -u defundd -f -o cat

# Escape from logs ctrl+c
```

```bash
# Check the logs again
journalctl -u defundd -f -o cat

# Escape from logs ctrl+c
```

**Now all ok, check the status**

```bash
# Check status
curl localhost:${DEFUND_PORT}657/status

"catching_up": false means that the node is synchronized, we are waiting for complete synchronization
```

**Wallet**

```bash
# Create wallet
defundd keys add wallet
```

Create a password for the wallet and write it down so you don't forget it. The wallet has been created. In the last line there will be a phrase that must be written down

```bash
# If the wallet was already there, restore it
defundd keys add wallet --recover
# Insert the seed phrase from your wallet
# If everything is correct, you will see your wallet data
```

Go to the [# ](https://discord.com/channels/913091321114296330/1038133368841310280)[faucet](https://discord.com/channels/913091321114296330/1038133368841310280) branch and request tokens

```bash
# Save the wallet address
# Replace <your_address> with your wallet address
DEFUND_ADDRESS=<your_address>
echo "export DEFUND_ADDRESS=$DEFUND_ADDRESS" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
bash# Check the ballance
defundd q bank balances $DEFUND_ADDRESS
```

**Validator**

Do not forget to create a profile on [https://keybase.io/](https://keybase.io/) and set a profile photo there that will be imported by key and used for your validators.

```bash
# Change <identity> to your key from keybase
defundd tx staking create-validator \
--amount 1000000ufetf \
--from=wallet \
--commission-rate "0.05" \
--commission-max-rate "0.20" \
--commission-max-change-rate "0.1" \
--min-self-delegation "1" \
--pubkey=$(defundd tendermint show-validator) \
--moniker=$DEFUND_MONIKER \
--chain-id=$DEFUND_CHAIN_ID \
--fees=5000ufetf \
--identity="" \
--details="" \
--website="" \
-y
```

Check yourself in the list [explorer](https://defund.explorers.guru/validators)

Or by command

```bash
defundd query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="$DEFUND_MONIKER")' | jq
```

```bash
# Edit the validator
defundd tx staking edit-validator \
  --new-moniker=$DEFUND_MONIKER \
  --website="" \
  --identity=<identity> \
  --details="" \
  --chain-id=$DEFUND_CHAIN_ID \
  --fees=5000ufetf \
  --from=wallet
```

```bash
# Save valoper_address in bash
# Change <your_valoper_address> to the address of the validator, starting with nibivaloper...
DEFUND_VALOPER=<your_valoper_address>
echo "export DEFUND_VALOPER=$DEFUND_VALOPER" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

!!! Save priv\_validator\_key.json which is located in /root/.defund/config
