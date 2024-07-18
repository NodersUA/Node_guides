# Installation (Validator Node)

## _**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/0g)
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
echo "export OG_CHAIN_ID=zgtendermint_16600-2" >> $HOME/.bash_profile
echo "export OG_PORT=47" >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```

```bash
# Download binary files
cd $HOME
git clone -b v0.2.3 https://github.com/0glabs/0g-chain.git
cd 0g-chain
make install

sudo cp $(which 0gchaind) /usr/local/bin/ && cd $HOME
0gchaind version --long | grep -e version -e commit
# 63c16892
```

```bash
# Initialize the node
0gchaind init $MONIKER --chain-id $OG_CHAIN_ID
```

```bash
# Download Genesis
wget https://github.com/0glabs/0g-chain/releases/download/v0.1.0/genesis.json -O $HOME/.0gchain/config/genesis.json

# Check Genesis
sha256sum $HOME/.0gchain/config/genesis.json

# cc862760abd4cebb91935e7506a745a251074cdd44148a5c9ae4ee7539dd8d0a
```

#### Change ports (Optional)

```bash
# Customize if you need
EXTERNAL_IP=$(wget -qO- eth0.me) \
PROXY_APP_PORT=${OG_PORT}658 \
P2P_PORT=${OG_PORT}656 \
PPROF_PORT=60${OG_PORT} \
API_PORT=13${OG_PORT} \
GRPC_PORT=9${OG_PORT}0 \
GRPC_WEB_PORT=9${OG_PORT}1
```

```bash
sed -i \
    -e "s/\(proxy_app = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$PROXY_APP_PORT\"/" \
    -e "s/\(laddr = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$RPC_PORT\"/" \
    -e "s/\(pprof_laddr = \"\)\([^:]*\):\([0-9]*\).*/\1localhost:$PPROF_PORT\"/" \
    -e "/\[p2p\]/,/^\[/{s/\(laddr = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$P2P_PORT\"/}" \
    -e "/\[p2p\]/,/^\[/{s/\(external_address = \"\)\([^:]*\):\([0-9]*\).*/\1${EXTERNAL_IP}:$P2P_PORT\"/; t; s/\(external_address = \"\).*/\1${EXTERNAL_IP}:$P2P_PORT\"/}" \
    $HOME/.0gchain/config/config.toml

sed -i \
    -e "/\[api\]/,/^\[/{s/\(address = \"tcp:\/\/\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$API_PORT\4/}" \
    -e "/\[grpc\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$GRPC_PORT\4/}" \
    -e "/\[grpc-web\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$GRPC_WEB_PORT\4/}" \
    -e "/\[json-rpc\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:8${OG_PORT}5\4/}" \
    -e "/\[json-rpc\]/,/^\[/{s/\(ws-address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:8${OG_PORT}6\4/}" \
    $HOME/.0gchain/config/app.toml
    
sed -i.bak -e "s%^node *=.*\"%node = \"tcp://${EXTERNAL_IP}:${OG_PORT}657\"%" \
    $HOME/.0gchain/config/client.toml
    
0gchaind config node tcp://127.0.0.1:${OG_PORT}657
```

**Setup config**

```bash
# correct config (so we can no longer use the chain-id flag for every CLI command in client.toml)
0gchaind config chain-id $OG_CHAIN_ID

# adjust if necessary keyring-backend в client.toml 
0gchaind config keyring-backend test

# Set the minimum price for gas
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0ua0gi\"/" ~/.0gchain/config/app.toml

# Add seeds/peers в config.toml
peers=""
sed -i -e "s|^persistent_peers *=.*|persistent_peers = \"$peers\"|" $HOME/.0gchain/config/config.toml
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.0gchain/config/config.toml

seeds="81987895a11f6689ada254c6b57932ab7ed909b6@54.241.167.190:26656,010fb4de28667725a4fef26cdc7f9452cc34b16d@54.176.175.48:26656,e9b4bc203197b62cc7e6a80a64742e752f4210d5@54.193.250.204:26656,68b9145889e7576b652ca68d985826abd46ad660@18.166.164.232:26656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.0gchain/config/config.toml

# Set up filter for "bad" peers
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.0gchain/config/config.toml

# Set up pruning
pruning="custom"
pruning_keep_recent="100"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.0gchain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.0gchain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.0gchain/config/app.toml

sed -i -e "s/^network *=.*/network = \"signet\"/" $HOME/.0gchain/config/app.toml
sed -i -e "s/^key-name *=.*/key-name = \"wallet\"/" ~/.0gchain/config/app.toml
sed -i -e "s/^timeout_commit *=.*/timeout_commit = \"30s\"/" ~/.0gchain/config/config.toml
```

**(OPTIONAL) Turn off indexing in config.toml**

```bash
indexer="kv" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.0gchain/config/config.toml
```

```bash
# Create service file (One command)
sudo tee /etc/systemd/system/Ogchaind.service > /dev/null <<EOF
[Unit]
Description=0G Node
After=network.target
 
[Service]
Type=simple
User=$USER
WorkingDirectory=$HOME/go/bin
ExecStart=/usr/local/bin/0gchaind start
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
systemctl enable Ogchaind
systemctl restart Ogchaind && journalctl -u Ogchaind -f -o cat

# Escape from logs ctrl+c
```

```bash
# Check the logs again
journalctl -u Ogchaind -f -o cat

# Escape from logs ctrl+c
```

**Now all ok, check the status**

```bash
# Check status
curl localhost:${OG_PORT}657/status

# "catching_up": false means that the node is synchronized, we are waiting for complete synchronization
```

**Wallet**

```bash
# Create wallet
0gchaind keys add wallet
```

The wallet has been created. In the last line there will be a phrase that must be written down

```bash
# If the wallet was already there, restore it
0gchaind keys add wallet --recover
# Insert the seed phrase from your wallet
# If everything is correct, you will see your wallet data
```

Go to the [faucet](https://faucet.0g.ai/) and request tokens to your evm address

```bash
# Check your EVM Address
echo "0x$(0gchaind debug addr $(0gchaind keys show wallet -a) | grep hex | awk '{print $3}')"
```

```bash
# Save the wallet address
OG_ADDRESS=$(0gchaind keys show wallet -a)
OG_VALOPER=$(0gchaind keys show wallet --bech val -a)
echo "export OG_ADDRESS="${OG_ADDRESS} >> $HOME/.bash_profile
echo "export OG_VALOPER="${OG_VALOPER} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
# Check the ballance
0gchaind query bank balances $(0gchaind keys show wallet -a)
```

**Validator**

Do not forget to create a profile on [https://keybase.io/](https://keybase.io/) and set a profile photo there that will be imported by key and used for your validators.

```bash
# Change <identity> to your key from keybase
0gchaind tx staking create-validator \
--amount 1000000ua0gi \
--from=wallet \
--commission-rate "0.1" \
--commission-max-rate "0.20" \
--commission-max-change-rate "0.1" \
--min-self-delegation "1" \
--pubkey=$(0gchaind tendermint show-validator) \
--moniker=$MONIKER \
--chain-id=$OG_CHAIN_ID \
--identity=<identity> \
--details="" \
--website="" \
--gas="auto" \
--gas-adjustment=1.4 \
-y
```

Check yourself in the list [explorer](https://chainscan-newton.0g.ai/) and fill out the [form](https://docs.google.com/forms/d/e/1FAIpQLScsa1lpn43F7XAydVlKK\_ItLGOkuz2fBmQaZjecDn76kysQsw/viewform?ts=6617a343)

Or by command

```bash
0gchaind query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="$MONIKER")' | jq
```

```bash
# Edit the validator
0gchaind tx staking edit-validator \
  --new-moniker=$MONIKER \
  --website="" \
  --identity=<identity> \
  --details="" \
  --chain-id=$OG_CHAIN_ID \
  --gas="auto" \
  --gas-adjustment=1.4 \
  --from=wallet \
  -y
```

!!! Save priv\_validator\_key.json which is located in /root/.0gchain/config
