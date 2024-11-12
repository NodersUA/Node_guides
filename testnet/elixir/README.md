# Elixir

## _**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/elixir)
```

## **Manual Installation**

#### Install docker <a href="#install-docker" id="install-docker"></a>

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
# Install Docker Compose
if ! [ -x "$(command -v docker-compose)" ]; then
sudo curl -L "https://github.com/docker/compose/releases/download/v2.2.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose 
sudo chmod +x /usr/local/bin/docker-compose 
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
docker-compose --version
else
echo $(docker-compose --version)
fi
```

Create new EVM wallet and copy privat key

```
STRATEGY_EXECUTOR_IP_ADDRESS=<Your IP address>
STRATEGY_EXECUTOR_DISPLAY_NAME=<Your moniker>
STRATEGY_EXECUTOR_BENEFICIARY=<Address for rewards>
SIGNER_PRIVATE_KEY=<Validator private key>
```

```bash
mkdir elixir && cd elixir

sudo tee validator.env > /dev/null <<EOF
ENV=testnet-3

STRATEGY_EXECUTOR_IP_ADDRESS=$STRATEGY_EXECUTOR_IP_ADDRESS
STRATEGY_EXECUTOR_DISPLAY_NAME=$STRATEGY_EXECUTOR_DISPLAY_NAME
STRATEGY_EXECUTOR_BENEFICIARY=$STRATEGY_EXECUTOR_BENEFICIARY
SIGNER_PRIVATE_KEY=$SIGNER_PRIVATE_KEY

EOF
```

```
docker pull elixirprotocol/validator:v3
```

```bash
docker run -d \
  --env-file validator.env \
  --name elixir \
  --restart unless-stopped \
  -p 17690:17690 \
  elixirprotocol/validator:v3
```

```bash
docker logs -fn100 elixir
```

* Connect your another wallet [here](https://testnet-3.elixir.xyz/) and claim 1000 MOCK (you need have ETH Sepolia in balance)
* Stake 1000 MOCK
* Click "Custom validator" and enter your validator address
* Click "Delegate"

