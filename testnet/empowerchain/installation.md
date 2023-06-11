# Installation

_**Automatic Installation**_

```bash
# Soon...
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

# go version go1.20.5 linux/amd64
```

```bash
# Set the variables

# Come up with the name of your node and replace it instead <your_moniker>
EMPOWER_MONIKER=<your_moniker>

echo 'export EMPOWER_MONIKER='$EMPOWER_MONIKER >> $HOME/.bash_profile
echo "export EMPOWER_CHAIN_ID=circulus-1" >> $HOME/.bash_profile
echo "export EMPOWER_PORT=20" >> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```

```bash
# Download binary files
cd $HOME
git clone https://github.com/EmpowerPlastic/empowerchain.git && cd empowerchain
git checkout v1.0.0-rc2
cd chain
make install

empowerd version --long | grep -e version -e commit
# 1.0.0-rc1
# commit: 1ff234c3069ffd9b32099b3b679eca895c3aae2e
```

```bash
# Initialize the node
empowerd init $EMPOWER_MONIKER --chain-id $EMPOWER_CHAIN_ID
```

```bash
# Download Genesis
wget -O ~/.empowerchain/config/genesis.json "https://github.com/EmpowerPlastic/empowerchain/blob/main/testnets/circulus-1/genesis.json"

# Check Genesis
sha256sum $HOME/.empowerchain/config/genesis.json

# ab6b4938cd08898087c2d3d245bd9fbc4bf1567fec26863524e0372d3b8fc227
```

```bash
# Set the ports

# config.toml
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${EMPOWER_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${EMPOWER_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${EMPOWER_PORT}061\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${EMPOWER_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${EMPOWER_PORT}660\"%" $HOME/.empowerchain/config/config.toml

# app.toml
sed -i.bak -e "s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${EMPOWER_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${EMPOWER_PORT}91\"%; s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:1${EMPOWER_PORT}7\"%" $HOME/.empowerchain/config/app.toml
sed -i.bak -e "s%^address = \"localhost:9090\"%address = \"localhost:${EMPOWER_PORT}90\"%; s%^address = \"localhost:9091\"%address = \"localhost:${EMPOWER_PORT}91\"%; s%^address = \"tcp://localhost:1317\"%address = \"tcp://localhost:1${EMPOWER_PORT}7\"%" $HOME/.empowerchain/config/app.toml

# client.toml
sed -i.bak -e "s%^node = \"tcp://localhost:26657\"%node = \"tcp://localhost:${EMPOWER_PORT}657\"%" $HOME/.empowerchain/config/client.toml

external_address=$(wget -qO- eth0.me)
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:${EMPOWER_PORT}656\"/" $HOME/.empowerchain/config/config.toml
```

**Setup config**

```bash
# correct config (so we can no longer use the chain-id flag for every CLI command in client.toml)
empowerd config chain-id $EMPOWER_CHAIN_ID

# adjust if necessary keyring-backend в client.toml 
empowerd config keyring-backend test

# Set the minimum price for gas
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.025umpwr\"/" $HOME/.empowerchain/config/app.toml

# Add seeds/peers в config.toml
peers="b897014f22e932e461e7fc98353a57d642dbe16e@empower-testnet.nodejumper.io:32656,264f7ae64579a2d7fbb07c6ea6fed90898f4c406@139.59.58.78:26056,da9776ea1bb96e66545c9e271f21724b04548d4d@65.109.154.182:52656,e0e26c9e0c93769fcc6684da566709d0dffb5092@95.217.23.88:26656,3761b399ad83a851e2829ab2e29fff732003232b@68.183.124.35:26656,bd850329ddc098a05d73ed96c429e47e1750911f@65.21.232.160:656,9489bf20423193157975ebda4af69defeaf5b5a0@65.109.116.204:22356,bcde171f8be8b3069726608d1e1fdde367d2e191@65.109.82.112:20456,865ba084f3f576df38984ae2bbee046f834d38dd@35.239.75.43:16656,64af1729ec239e6ac31fb77f50950e81a0e95b13@172.93.110.154:28656,8a2dd7c4d01ff6a78e05734a938097b01f0df8c8@84.39.241.20:36656,db48aa6e227fcc3d3b921dfac0d3d0f6decf8338@212.90.121.106:26656,f95be62dddc6c3d962e229a19a8384892b2a2f64@95.217.160.106:15056,ef4e2705d08fe79172381de8507c1c72758bb441@65.21.82.203:60556,70e90f32a86db93ce97dc4f691244b9b2188fc5f@37.27.8.22:26656,50f3cb8b0f11e71af102c9d41abd183b2116eb6e@164.92.165.194:26656,9595954bb0f8703dbc9db54364faeaa5e779c495@85.239.236.13:15056,132dea83b3f0d899329c940f18466ab8e17f1cef@31.220.89.98:26656,4f77ebcaedbef250ba6d8d63c376f6b1eec31b04@109.236.92.76:17456,02e17aac82fae9fba479065669871861fc925400@5.161.86.216:12656,e855b3e36e9506695ffc39a180af8c0453928b7c@89.117.56.126:24356,95ea7999e3ecd3fb7fd73fae70b3b29a6af24c8d@46.4.5.45:17456,808a8eba7e272401795868ad0bbebb23d2bb7eb5@65.108.238.147:32656,f6162c0ee44669eeae9ffeee201d84de2d04adc1@167.235.180.97:17456,72fe41cd9bec8f1a7cdf88fc81001fc9c7ea49e2@49.13.3.215:15056,66fa8b854f03f3c218e5c3cb18f7f822d72f1fa2@213.133.99.244:26656,a42595cd22c9491a89386f83385398bdac65b16a@104.248.143.31:26656,619f818a1ad3cb100c24a24cd5f393459e526101@173.249.49.114:15056,2304a3a885a1c2ced7453a79c445979f425b821d@65.108.199.206:32656,9e4aba1835f3590da434558daa3b1baeddff41dc@184.174.37.152:35656,48ffb42208b80f9f9b8ee5e9f8d4e50ba5cfb5e9@38.242.129.109:15056,67da1cce2ced3531948395690c6feb8cd4ed81fd@65.108.231.238:32656,f2d9830865586f194f4437878f2e112c57caedf5@95.217.6.63:15056,304a2af3db879b52a8baf571e6ec7ff1b13de93c@65.108.250.118:15056,c89be50ba68fafe691b15e8833988cb79297dab2@185.209.230.127:15056,d5c47557c82a74a08b4638c9332377320291255f@85.239.234.218:26656,255502c571df61a8eeba9af57f57324ba65c57ff@65.21.141.82:26656,5e8a0ef0c941f7b68f45610cf280ccc1a208e6d0@65.109.33.48:24656,f05e7b521d8c779514d0c0dc5b886375d0805bed@54.39.128.229:26646,57aeb2ff8f976942ef3d4b6406b0d4eb1e62231f@5.161.103.120:26656,d90b17e15e891ee729b1608bccc654635502ee9a@143.198.195.2:26656,d2de4376a83e34300bf3946666a8b675f8a033b8@184.105.162.170:26656,1fcc636454412a3ada561b69399704e1d1e3be3a@149.102.131.176:15056,3fdbae8d720dfcdeadd631f46db06ba222515e96@65.109.89.35:26656,c1caa84f93f9e3bae30f51889ef6f4b588262fcf@65.108.72.233:42656,1d9a92feae6c392ca218b07175a602c996cd28b6@109.236.82.5:22056,8cd17dfb379be4b2de810ca98b8af541d699dc1f@5.161.184.204:26656,0835b028b7532599dd95fe97d1c01ea420b5bd0b@65.21.225.10:52656,22f43ae42bc61f3cc26af28ed545d40a6e4834ac@195.201.239.70:26656,17a2d8b6ab329e60eba16abc17eccb1342f49a04@85.10.197.4:50656,046d5f53f1c9c28a832da7b70e0e1eda24480d70@78.160.231.135:35656"
sed -i -e "s|^persistent_peers *=.*|persistent_peers = \"$peers\"|" $HOME/.empowerchain/config/config.toml
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.empowerchain/config/config.toml

seeds="c597ec01e412d6e0f62c6f5501224b7fb8393912@empower-testnet-seed.itrocket.net:16656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.empowerchain/config/config.toml

# Set up filter for "bad" peers
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.empowerchain/config/config.toml

# Set up pruning
pruning="nothing"
pruning_keep_recent="100"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.empowerchain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.empowerchain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.empowerchain/config/app.toml
```

**(OPTIONAL) Turn off indexing in config.toml**

```bash
indexer="null" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.empowerchain/config/config.toml
```

```bash
# Create service file (One command)
sudo tee /etc/systemd/system/empowerd.service > /dev/null <<EOF
[Unit]
Description=EmpowerChain Node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which empowerd) start
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

```bash
# Start the node
systemctl daemon-reload
systemctl enable empowerd
systemctl restart empowerd && journalctl -u empowerd -f -o cat

# Escape from logs ctrl+c
```

```bash
# Check the logs again
journalctl -u empowerd -f -o cat

# Escape from logs ctrl+c
```

```bash
# Check the blocks
empowerd status 2>&1 | jq ."SyncInfo"."latest_block_height"
```

Check height in [explorer](https://empower.explorers.guru/)

**Wallet**

```bash
# Create wallet
empowerd keys add wallet
```

The wallet has been created. In the last line there will be a phrase that must be written down

```bash
# If the wallet was already there, restore it
empowerd keys add wallet --recover
# Insert the seed phrase from your wallet
# If everything is correct, you will see your wallet data
```

Go to the [#faucet](https://discord.com/channels/948213834164883488/1026598604523180043) branch and request tokens

```bash
# Save the wallet address
EMPOWER_ADDRESS=$(empowerd keys show wallet -a)
EMPOWER_VALOPER=$(empowerd keys show wallet --bech val -a)
echo "export EMPOWER_ADDRESS="${EMPOWER_ADDRESS} >> $HOME/.bash_profile
echo "export EMPOWER_VALOPER="${EMPOWER_VALOPER} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
bash# Check the ballance
empowerd query bank balances $EMPOWER_ADDRESS
```

**Validator**

Do not forget to create a profile on [https://keybase.io/](https://keybase.io/) and set a profile photo there that will be imported by key and used for your validators.

```bash
# Change <identity> to your key from keybase
empowerd tx staking create-validator \
    --amount 1000000umpwr \
    --chain-id $EMPOWER_CHAIN_ID \
    --commission-max-change-rate 0.1 \
    --commission-max-rate 0.2 \
    --commission-rate 0.05 \
    --min-self-delegation "1" \
    --moniker $EMPOWER_MONIKER \
    --details="<validator-description" \
    --website "<validator-website>" \
    --identity="<keybase-id>" \
    --pubkey=$(empowerd tendermint show-validator) \
    --gas-prices 0.025umpwr \
    --from wallet \
    --y
```

Check yourself in the list [explorer](https://empower.explorers.guru/validators)

Or by command

```bash
empowerd query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="$EMPOWER_MONIKER")' | jq
```

```bash
# Edit the validator
empowerd tx staking edit-validator \
  --new-moniker=$EMPOWER_MONIKER \
  --website="" \
  --identity=<identity> \
  --details="" \
  --chain-id=$EMPOWER_CHAIN_ID \
  --gas auto \
  --gas-adjustment=1.2 \
  --gas-prices 0.025umpwr \
  --from=wallet
```

!!! Save priv\_validator\_key.json which is located in /root/.empowerchain/config
