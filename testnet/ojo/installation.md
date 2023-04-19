**Automatic Installation**
```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/ojo) 
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
MONIKER_OJO=<your_moniker>

echo 'export MONIKER_OJO='$MONIKER_OJO >> $HOME/.bash_profile
echo "export CHAIN_ID_OJO=ojo-devnet" >> $HOME/.bash_profile
echo "export PORT_OJO=37" >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```
```bash
# Download binary files
cd $HOME 
git clone https://github.com/ojo-network/ojo
cd ojo
git fetch --all 
git checkout v0.1.2
make install
sudo cp $HOME/go/bin/ojod /usr/local/bin/ojod
ojod version --long | grep -e version -e commit -e build
# HEAD-ad5a2377134aa13d7d76575b95613cf8ed12d1e4
# commit: ad5a2377134aa13d7d76575b95613cf8ed12d1e4
# v0.1.2
```
```bash
# Initialize the node
ojod init $MONIKER_OJO --chain-id $CHAIN_ID_OJO
```
```bash
# Download Genesis
curl -Ls https://rpc.devnet-n0.ojo-devnet.node.ojo.network/genesis > $HOME/.ojo/config/genesis.json

# Check Genesis
sha256sum $HOME/.ojo/config/genesis.json
```
```bash
# Set the ports

# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${PORT_OJO}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${PORT_OJO}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${PORT_OJO}061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${PORT_OJO}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${PORT_OJO}660\"%" $HOME/.ojo/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${PORT_OJO}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${PORT_OJO}91\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${PORT_OJO}27\"%" $HOME/.ojo/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${PORT_OJO}657\"%" $HOME/.ojo/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:${PORT_OJO}656\"/" $HOME/.ojo/config/config.toml
```

***Setup config***
```bash
# correct config (so we can no longer use the chain-id flag for every CLI command in client.toml)
ojod config chain-id ojo-devnet

# adjust if necessary keyring-backend в client.toml 
ojod config keyring-backend test

ojod config node tcp://localhost:${PORT_OJO}657

# Set the minimum price for gas
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025uojo\"/;" ~/.ojo/config/app.toml

# Add seeds/peers в config.toml
peers=""
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.ojo/config/config.toml

seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.ojo/config/config.toml

# Set up filter for "bad" peers
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.ojo/config/config.toml

#
sed -i -e "s/^timeout_commit *=.*/timeout_commit = \"10s\"/" $HOME/.ojo/config/config.toml
sed -i -e "s/^timeout_propose *=.*/timeout_propose = \"10s\"/" $HOME/.ojo/config/config.toml
sed -i -e "s/^create_empty_blocks_interval *=.*/create_empty_blocks_interval = \"10s\"/" $HOME/.ojo/config/config.toml
sed -i 's/create_empty_blocks = .*/create_empty_blocks = true/g' ~/.ojo/config/config.toml
sed -i 's/timeout_broadcast_tx_commit = ".*s"/timeout_broadcast_tx_commit = "10s"/g' ~/.ojo/config/config.tom

# Set up pruning
pruning="custom"
pruning_keep_recent="1000"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.ojo/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.ojo/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.ojo/config/app.toml
```

***(OPTIONAL) Turn off indexing in config.toml***
```bash
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.ojo/config/config.toml
```

***Cosmovisor***
```bash
# Install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest
```
```bash
# Create directories
mkdir -p ~/.ojo/cosmovisor
mkdir -p ~/.ojo/cosmovisor/genesis
mkdir -p ~/.ojo/cosmovisor/genesis/bin
mkdir -p ~/.ojo/cosmovisor/upgrades
```
```bash
# Copy the binary file to the cosmovisor folder
cp `which ojod` ~/.ojo/cosmovisor/genesis/bin/ojod
```
```bash
# Create service file (One command)
sudo tee /etc/systemd/system/ojod.service > /dev/null <<EOF
[Unit]
Description=ojod daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start --x-crisis-skip-assert-invariants
Restart=always
RestartSec=3
LimitNOFILE=infinity

Environment="DAEMON_NAME=ojod"
Environment="DAEMON_HOME=${HOME}/.ojo"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"

[Install]
WantedBy=multi-user.target
EOF
```
```bash
# Start the node
systemctl daemon-reload
systemctl enable ojod
systemctl restart ojod && journalctl -u ojod -f -o cat

# Escape from logs ctrl+c
```
```bash
# Check the logs again
journalctl -u ojod -f -o cat

# Escape from logs ctrl+c
```

Now all ok, Check status
```bash
ojod status 2>&1 | jq "{catching_up: .SyncInfo.catching_up}"
"catching_up": false means that the node is synchronized, we are waiting for complete synchronization
```

***Wallets***
```bash
# Create wallet
ojod keys add wallet
```

Create a password for the wallet and write it down so you don't forget it. The wallet has been created. In the last line there will be a phrase that must be written down
```bash
# If the wallet was already there, restore it
ojod keys add wallet --recover
# Insert the seed phrase from your wallet
# If everything is correct, you will see your wallet data
```

Go to the # faucet branch and request tokens
```bash
# Save the wallet address
WALLET_OJO=$(ojod keys show wallet -a)
VALOPER_OJO=$(ojod keys show wallet --bech val -a)
echo "export WALLET_OJO="${WALLET_OJO} >> $HOME/.bash_profile
echo "export VALOPER_OJO="${VALOPER_OJO} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
# Check the ballance
ojod q bank balances $WALLET_OJO
```

***Validator***

Do not forget to create a profile on https://keybase.io/ and set a profile photo there that will be imported by key and used for your validators.
```bash
# Change <identity> to your key from keybase
ojod tx staking create-validator \
--amount 1000000uojo \
--from=wallet \
--commission-rate "0.05" \
--commission-max-rate "0.20" \
--commission-max-change-rate "0.1" \
--min-self-delegation "1" \
--pubkey=$(ojod tendermint show-validator) \
--moniker=$MONIKER_OJO \
--chain-id=$CHAIN_ID_OJO \
--fees=5000uojo \
--identity=<identity> \
--details="" \
--website="" \
-y
```

Check yourself in the list explorer

Or by command
```bash
ojod query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="$MONIKER_OJO")' | jq
```
```bash
# Edit the validator
ojod tx staking edit-validator \
  --new-moniker=$MONIKER_OJO \
  --website="" \
  --identity=<identity> \
  --details="" \
  --chain-id=$CHAIN_ID_OJO \
  --fees=1000uojo \
  --from=wallet
```
!!! Save priv_validator_key.json which located in /root/.ojo/config
