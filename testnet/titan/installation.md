# Installation (Validator Node)

## _**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/titan)
```

## **Manual Installation**

### Setting up a 0g node

```bash
# Install developer packages
sudo apt update && \
sudo apt install curl git jq build-essential gcc unzip wget lz4 -y
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
echo "export TITAN_CHAIN_ID=titan-test-3" >> $HOME/.bash_profile
echo "export TITAN_PORT=48" >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```

```bash
# Download binary files
cd $HOME
git clone https://github.com/Titannet-dao/titan-chain.git
cd titan-chain
git checkout v2.1.0
make install

sudo cp $(which titand) /usr/local/bin/ && cd $HOME
```

```bash
# Initialize the node
titand init $MONIKER --chain-id $TITAN_CHAIN_ID
```

```bash
# Download Genesis
wget https://raw.githubusercontent.com/Titannet-dao/titan-chain/main/genesis/genesis.json -O $HOME/.titan/config/genesis.json

# Download Addrbook
wget -O $HOME/.titan/config/addrbook.json "https://raw.githubusercontent.com/111STAVR111/props/main/Titan/addrbook.json"

# Check Genesis
sha256sum $HOME/.titan/config/genesis.json

# bd9413266b4e5fe737b80a246f1afa1cb604d52be4d31a4fbb7d04eb5cfc339d
```

#### Change ports (Optional)

```bash
# Customize if you need
EXTERNAL_IP=$(wget -qO- eth0.me) \
PROXY_APP_PORT=${TITAN_PORT}658 \
P2P_PORT=${TITAN_PORT}656 \
PPROF_PORT=60${TITAN_PORT} \
API_PORT=13${TITAN_PORT} \
GRPC_PORT=9${TITAN_PORT}0 \
GRPC_WEB_PORT=9${TITAN_PORT}1
```

```bash
sed -i \
    -e "s/\(proxy_app = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$PROXY_APP_PORT\"/" \
    -e "s/\(laddr = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$RPC_PORT\"/" \
    -e "s/\(pprof_laddr = \"\)\([^:]*\):\([0-9]*\).*/\1localhost:$PPROF_PORT\"/" \
    -e "/\[p2p\]/,/^\[/{s/\(laddr = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$P2P_PORT\"/}" \
    -e "/\[p2p\]/,/^\[/{s/\(external_address = \"\)\([^:]*\):\([0-9]*\).*/\1${EXTERNAL_IP}:$P2P_PORT\"/; t; s/\(external_address = \"\).*/\1${EXTERNAL_IP}:$P2P_PORT\"/}" \
    $HOME/.titan/config/config.toml

sed -i \
    -e "/\[api\]/,/^\[/{s/\(address = \"tcp:\/\/\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$API_PORT\4/}" \
    -e "/\[grpc\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$GRPC_PORT\4/}" \
    -e "/\[grpc-web\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$GRPC_WEB_PORT\4/}" \
    -e "/\[json-rpc\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:8${TITAN_PORT}5\4/}" \
    -e "/\[json-rpc\]/,/^\[/{s/\(ws-address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:8${TITAN_PORT}6\4/}" \
    $HOME/.titan/config/app.toml
    
sed -i.bak -e "s%^node *=.*\"%node = \"tcp://${EXTERNAL_IP}:${TITAN_PORT}657\"%" \
    $HOME/.titan/config/client.toml
    
#titand config node tcp://127.0.0.1:${TITAN_PORT}657
```

**Setup config**

```bash
# correct config (so we can no longer use the chain-id flag for every CLI command in client.toml)
#titand config chain-id $TITAN_CHAIN_ID

# adjust if necessary keyring-backend в client.toml 
#titand config keyring-backend test

# Set the minimum price for gas
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0025uttnt\"/" ~/.titan/config/app.toml

# Add seeds/peers в config.toml
peers=""
sed -i -e "s|^persistent_peers *=.*|persistent_peers = \"$peers\"|" $HOME/.titan/config/config.toml
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.titan/config/config.toml

seeds="bb075c8cc4b7032d506008b68d4192298a09aeea@47.76.107.159:26656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.titan/config/config.toml

# Set up filter for "bad" peers
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.titan/config/config.toml

# Set up pruning
pruning="custom"
pruning_keep_recent="1000"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.titan/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.titan/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.titan/config/app.toml

sed -i -e "s/^network *=.*/network = \"signet\"/" $HOME/.titan/config/app.toml
sed -i -e "s/^key-name *=.*/key-name = \"wallet\"/" ~/.titan/config/app.toml
sed -i -e "s/^timeout_commit *=.*/timeout_commit = \"30s\"/" ~/.titan/config/config.toml
```

**(OPTIONAL) Turn off indexing in config.toml**

```bash
indexer="null" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.titan/config/config.toml
```

```bash
# Create service file (One command)
sudo tee /etc/systemd/system/titand.service > /dev/null <<EOF
[Unit]
Description=Titan Node
After=network.target
 
[Service]
Type=simple
User=$USER
WorkingDirectory=$HOME/go/bin
ExecStart=/usr/local/bin/titand start
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
systemctl enable titand
systemctl restart titand && journalctl -u titand -f -o cat

# Escape from logs ctrl+c
```

```bash
# Check the logs again
journalctl -u titand -f -o cat

# Escape from logs ctrl+c
```

**Now all ok, check the status**

```bash
# Check status
curl localhost:${TITAN_PORT}657/status

# "catching_up": false means that the node is synchronized, we are waiting for complete synchronization
```

**Wallet**

```bash
# Create wallet
titand keys add wallet --keyring-backend test
```

The wallet has been created. In the last line there will be a phrase that must be written down

```bash
# If the wallet was already there, restore it
titand keys add wallet --keyring-backend test --recover
# Insert the seed phrase from your wallet
# If everything is correct, you will see your wallet data
```

Go to the [faucet](https://faucet.0g.ai/) and request tokens to your evm address

```bash
# Check your EVM Address
echo "0x$(titand debug addr $(titand keys show wallet --keyring-backend test -a) | grep hex | awk '{print $3}')"
```

```bash
# Save the wallet address
TITAN_ADDRESS=$(titand keys show wallet --keyring-backend test -a)
TITAN_VALOPER=$(titand keys show wallet --keyring-backend test --bech val -a)
echo "export TITAN_ADDRESS="${TITAN_ADDRESS} >> $HOME/.bash_profile
echo "export TITAN_VALOPER="${TITAN_VALOPER} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
# Check the ballance
titand query bank balances $(titand keys show wallet --keyring-backend test -a)
```

**Validator**

Do not forget to create a profile on [https://keybase.io/](https://keybase.io/) and set a profile photo there that will be imported by key and used for your validators.

```bash
sudo tee /root/.titan/validator.json > /dev/null <<EOF
{
 "pubkey":  "$(titand tendermint show-validator)",
 "amount": "1000000uttnt",
 "moniker": "$MONIKER",
 "commission-rate": "0.1",
 "commission-max-rate": "1.0",
 "commission-max-change-rate": "0.1",
 "min-self-delegation": "1",
 "identity": "$IDENTITY",
 "website": "$WEBSITE",
 "security": "",
 "details": ""
}
EOF
```

Check yourself in the list [explorer](https://chainscan-newton.0g.ai/) and fill out the [form](https://docs.google.com/forms/d/e/1FAIpQLScsa1lpn43F7XAydVlKK\_ItLGOkuz2fBmQaZjecDn76kysQsw/viewform?ts=6617a343)

Or by command

```bash
titand query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="$MONIKER")' | jq
```

```bash
# Edit the validator
titand tx staking edit-validator \
  --new-moniker=$MONIKER \
  --website="" \
  --identity=<identity> \
  --details="" \
  --chain-id=$TITAN_CHAIN_ID \
  --gas="auto" \
  --gas-adjustment=1.4 \
  --from=wallet \
  -y
```

!!! Save priv\_validator\_key.json which is located in /root/.0gchain/config
