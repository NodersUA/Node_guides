# Installation (Lava-testnet)

_**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/lava)
```

**Manual Installation**

```bash
# Update the repositories
apt update && apt upgrade -y
```

```bash
# Install developer packages
sudo apt -qy install curl git jq lz4 build-essential
```

```bash
#INSTALL GO
if [ "$(go version)" != "go version go1.20.5 linux/amd64" ]; then \
ver="1.20.5" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile ; \
fi
go version
```

```bash
# Set the variables

# Come up with the name of your node and replace it instead <your_moniker>
MONIKER=<your_moniker>

echo 'export MONIKER='$MONIKER >> $HOME/.bash_profile
echo "export LAVA_CHAIN_ID=lava-testnet-2" >> $HOME/.bash_profile
echo "export LAVA_PORT=33" >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```

```bash
# Download binary files
cd $HOME
rm -rf lava
git clone https://github.com/lavanet/lava.git && cd lava
git checkout v0.25.2
make install-all

sudo cp $(which lavad) /usr/local/bin/ && cd $HOME
lavad version
# 0.25.2
```

```bash
# Initialize the node
lavad init $MONIKER --chain-id $LAVA_CHAIN_ID
```

```bash
# Download Genesis
curl -Ls http://snapshots.stakevillage.net/snapshots/lava-testnet-2/genesis.json > $HOME/.lava/config/genesis.json
```

```bash
# Set the ports

# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${LAVA_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${LAVA_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${LAVA_PORT}061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${LAVA_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${LAVA_PORT}660\"%" $HOME/.lava/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${LAVA_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${LAVA_PORT}91\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1${LAVA_PORT}7\"%" $HOME/.lava/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${LAVA_PORT}657\"%" $HOME/.lava/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:${LAVA_PORT}656\"/" $HOME/.lava/config/config.toml
```

```bash
# correct config (so we can no longer use the chain-id flag for every CLI command in client.toml)
lavad config chain-id $LAVA_CHAIN_ID

# adjust if necessary keyring-backend в client.toml 
lavad config keyring-backend test

# Set the minimum price for gas
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0001ulava\"/" ~/.lava/config/app.toml

# Add seeds/peers в config.toml
peers=""
sed -i -e "s|^persistent_peers *=.*|persistent_peers = \"$peers\"|" $HOME/.lava/config/config.toml
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.lava/config/config.toml

seeds="3a445bfdbe2d0c8ee82461633aa3af31bc2b4dc0@testnet2-seed-node.lavanet.xyz:26656,e593c7a9ca61f5616119d6beb5bd8ef5dd28d62d@testnet2-seed-node2.lavanet.xyz:26656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.lava/config/config.toml

# Set up filter for "bad" peers
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.lava/config/config.toml

# Settings
sed -i -e "s|^timeout_commit *=.*|timeout_commit = \"30s\"|" $HOME/.lava/config/config.toml
sed -i -e "s|^timeout_propose *=.*|timeout_propose = \"1s\"|" $HOME/.lava/config/config.toml
sed -i -e "s|^timeout_precommit *=.*|timeout_precommit = \"1s\"|" $HOME/.lava/config/config.toml

sed -i -e "s|^timeout_precommit_delta *=.*|timeout_precommit_delta = \"500ms\"|" $HOME/.lava/config/config.toml
sed -i -e "s|^timeout_prevote *=.*|timeout_prevote = \"1s\"|" $HOME/.lava/config/app.toml

sed -i -e "s|^timeout_prevote_delta *=.*|timeout_prevote_delta = \"500ms\"|" $HOME/.lava/config/config.toml
sed -i -e "s|^timeout_propose_delta *=.*|timeout_propose_delta = \"500ms\"|" $HOME/.lava/config/config.toml
sed -i -e "s|^skip_timeout_commit *=.*|skip_timeout_commit = false|" $HOME/.lava/config/config.toml

# Set pruning
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.lava/config/app.toml
```

```bash
# Download latest chain snapshot
wget -O snapshot_latest.tar.lz4 http://snapshots.stakevillage.net/snapshots/lava-testnet-2/snapshot_latest.tar.lz4
tar -Ilz4 -xf snapshot_latest.tar.lz4 -C $HOME/.lava
rm snapshot_latest.tar.lz4
```

```bash
# Create service file (One command)
sudo tee /etc/systemd/system/lavad.service > /dev/null << EOF
[Unit]
Description=Lava node service
After=network-online.target
[Service]
User=$USER
ExecStart=$(which lavad) start
Restart=on-failure
RestartSec=10
LimitNOFILE=10000
[Install]
WantedBy=multi-user.target
EOF
```

```bash
# Start the node
systemctl daemon-reload
systemctl enable lavad
systemctl restart lavad && journalctl -u lavad -f -o cat

# Escape from logs ctrl+c
```

```bash
# Check the logs again
journalctl -u lavad -f -o cat

# Escape from logs ctrl+c
```
