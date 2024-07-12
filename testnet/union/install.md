# Install

### Setting up a Union node

```bash
# Set the variables

# Come up with the name of your node and replace it instead <your_moniker>
MONIKER=<your_moniker>

echo 'export MONIKER='$MONIKER >> $HOME/.bash_profile
echo "export UNION_PORT=48" >> $HOME/.bash_profile
echo "export UNION_CHAIN_ID=union-testnet-8" >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```

```bash
# Install Docker
if ! [ -x "$(command -v docker)" ]; then
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo usermod -aG docker $USER
docker --version
else
echo $(docker --version)
fi
```

```bash
UNIOND_VERSION=v0.23.0
```

```bash
docker pull ghcr.io/unionlabs/uniond-release:$UNIOND_VERSION
```

```bash
mkdir ~/.union
docker run \
  --user $(id -u):$(id -g) \
  --volume ~/.union:/.union \
  --volume /tmp:/tmp \
  -it ghcr.io/unionlabs/uniond-release:$UNIOND_VERSION init $MONIKER \
  --home /.union
```

```bash
alias uniond='docker run -v ~/.union:/.union -v /tmp:/tmp --network host -it ghcr.io/unionlabs/uniond-release:$UNIOND_VERSION --home /.union'
```

```bash
cd $HOME
wget -O uniond https://snapshots.nodes.guru/union/uniond
sudo chmod +x uniond
sudo mv ./uniond /usr/local/bin/
```

```bash
#uniond init $MONIKER --chain-id $UNION_CHAIN_ID
```

```bash
seeds="c2bf0d5b2ad3a1df0f4e9cc32debffa239c0af90@testnet.seed.poisonphang.com:26656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" ~/.union/config/config.toml
```

#### Change ports (Optional)

```bash
# Customize if you need
EXTERNAL_IP=$(wget -qO- eth0.me) \
RPC_PORT=${UNION_PORT}657 \
PROXY_APP_PORT=${UNION_PORT}658 \
P2P_PORT=${UNION_PORT}656 \
PPROF_PORT=60${UNION_PORT} \
API_PORT=13${UNION_PORT} \
GRPC_PORT=9${UNION_PORT}0 \
GRPC_WEB_PORT=9${UNION_PORT}1
```

```bash
sed -i \
    -e "s/\(proxy_app = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$PROXY_APP_PORT\"/" \
    -e "s/\(laddr = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$RPC_PORT\"/" \
    -e "s/\(pprof_laddr = \"\)\([^:]*\):\([0-9]*\).*/\1localhost:$PPROF_PORT\"/" \
    -e "/\[p2p\]/,/^\[/{s/\(laddr = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$P2P_PORT\"/}" \
    -e "/\[p2p\]/,/^\[/{s/\(external_address = \"\)\([^:]*\):\([0-9]*\).*/\1${EXTERNAL_IP}:$P2P_PORT\"/; t; s/\(external_address = \"\).*/\1${EXTERNAL_IP}:$P2P_PORT\"/}" \
    $HOME/.union/config/config.toml

sed -i \
    -e "/\[api\]/,/^\[/{s/\(address = \"tcp:\/\/\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$API_PORT\4/}" \
    -e "/\[grpc\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$GRPC_PORT\4/}" \
    -e "/\[grpc-web\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$GRPC_WEB_PORT\4/}" \
    $HOME/.union/config/app.toml
    
#uniond config node tcp://127.0.0.1:${UNION_PORT}657
```

```bash
curl "https://snapshots.nodes.guru/union/genesis.json" > ~/.union/config/genesis.json
```

```bash
// Some code# Set the minimum price for gas
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0muno\"/" ~/.union/config/app.toml

# Set up filter for "bad" peers
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.union/config/config.toml

# Set up pruning
pruning="default"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.union/config/app.toml
```

```bash
# Create service file (One command)
sudo tee /etc/systemd/system/uniond.service > /dev/null <<EOF
[Unit]
Description=Union Node
After=network.target
 
[Service]
Type=simple
User=$USER
WorkingDirectory=$HOME/.union
ExecStart=$(which uniond) start --home $HOME/.union
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
systemctl enable uniond
systemctl restart uniond && journalctl -u uniond -f -o cat

# Escape from logs ctrl+c
```

**Wallet**

```bash
# Create wallet
uniond keys add wallet --keyring-backend test --home ~/.union/
```

The wallet has been created. In the last line there will be a phrase that must be written down

```bash
# If the wallet was already there, restore it
uniond keys add wallet --recover --keyring-backend test --home ~/.union/
# Insert the seed phrase from your wallet
# If everything is correct, you will see your wallet data
```

```bash
uniond q bank balances $(uniond keys show wallet -a --keyring-backend test --home $HOME/.union) --node https://rpc-1.testnet.union.nodes.guru:443
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

