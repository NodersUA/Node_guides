# Elixir

## _**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/elixir)
```

## **Manual Installation**

Create new EVM wallet and copy privat key

```
STRATEGY_EXECUTOR_IP_ADDRESS=<Your IP address>
STRATEGY_EXECUTOR_DISPLAY_NAME=<Your moniker>
STRATEGY_EXECUTOR_BENEFICIARY=<Address for rewards>
SIGNER_PRIVATE_KEY=<Validator private key>
```

```bash
mkdir elixir

sudo tee /etc/systemd/system/rivalz.service > /dev/null <<EOF
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
docker run -it \
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

