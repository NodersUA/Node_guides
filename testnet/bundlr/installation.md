# Installation

_**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/bundlr/bundlr)
```

**Manual Installation**
## Install Pre-requisite Software
```bash
# Update the repositories
apt update && apt upgrade -y
```

```bash
# Install developer packages
sudo apt install curl ncdu htop git wget build-essential libssl-dev libpq-dev gcc make libssl-dev pkg-config npm expect -y
```

```bash
# Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo usermod -aG docker $USER
docker --version
```

```bash
# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.2.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose 
sudo chmod +x /usr/local/bin/docker-compose 
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
docker-compose --version
```

```bash
# Install Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
source "$HOME/.cargo/env"
```

```bash
# Install NVM on Ubuntu
curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash 
source ~/.bashrc
nvm install node
```

## Download Repository and Set-up
```bash
git clone --recurse-submodules https://github.com/Bundlr-Network/validator.git bundlr && cd bundlr
cargo run --bin wallet-tool create | tee wallet.json |  cargo run --bin wallet-tool -- show-address
```
```bash
echo "export BUNDLR_ADDRESS=<YOUR_ADDRESS>" >> ~/.bash_profile
source ~/.bash_profile
```
_**!!! Save your address and file ~/bundlr/wallet.json**_

```bash
BUNDLER_PORT=42069
BUNDLER_URL=https://testnet1.bundlr.network
GW_CONTRACT="RkinCLBlY4L5GZFv8gCFcrygTyd5Xm91CzKlR6qxhKA"
GW_ARWEAVE=https://arweave.testnet1.bundlr.network
GW_STATE_ENDPOINT=https://faucet.testnet1.bundlr.network
source ~/.bash_profile

tee .env > /dev/null <<EOF
PORT=$BUNDLER_PORT
BUNDLER_URL=$BUNDLER_URL
GW_CONTRACT=$GW_CONTRACT
GW_ARWEAVE=$GW_ARWEAVE
GW_STATE_ENDPOINT=$GW_STATE_ENDPOINT
EOF
```

## Build and Run the Validator
```bash
cd $HOME/bundlr
sed -i 's/source: ${PWD}\/wallet.json/source: .\/wallet.json/g' docker-compose.yml
sudo docker-compose up -d
```

## Register Validator and Stake
```bash
sudo npm i -g @bundlr-network/testnet-cli@latest
```
You can claim Testnet tokens [here](https://bundlr.network/faucet), you will need a valid twitter account and the Arweave wallet address created earlier.
```bash
# Check ballance
npx @bundlr-network/testnet-cli@latest balance $BUNDLR_ADDRESS
```
```bash
# Register Validator and Stake
npx @bundlr-network/testnet-cli@latest join $GW_CONTRACT -w $HOME/bundlr/wallet.json -u http://$(wget -qO- eth0.me):$BUNDLER_PORT -s <stake-tokens>
```

```bash
# Check validator is active
npx @bundlr-network/testnet-cli@latest check $GW_CONTRACT $BUNDLR_ADDRESS
```
