# Installation

## Step 1: Deploy your Operator <a href="#step-1-deploy-your-operator" id="step-1-deploy-your-operator"></a>

1. Visit [this site](https://mumbai.streamr.network/hub/network/operators) and connect your Owner Metamask wallet
2. Request test tokens from the [faucet](https://faucet.polygon.technology/)
3. Click "Become an Operator" and complete the dialog
4.  Join [Discord](https://discord.gg/gZAm8P7hK8) and request DATA test tokens in [# faucet-bot](https://discord.com/channels/801574432350928907/1169944445203001354)\


    <figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>
5. Import token to MetaMask (Contract: 0x53d8268307c6EE131AafDe5E6FD3575bADbB3D20)

## Step 2: Run a Streamr node <a href="#step-2-run-a-streamr-node" id="step-2-run-a-streamr-node"></a>

_**Automatic Installation**_

```bash
# Soon...
# source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/streamer)
```

_**Manual Installation**_

```bash
# Update the repositories
apt update && apt upgrade -y
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
# Run the config wizard
mkdir ~/.streamrDocker
sudo chmod -R 777 ~/.streamrDocker/
sudo docker run -it -v $(cd ~/.streamrDocker && pwd):/home/streamr/.streamr streamr/broker-node:v100.0.0-testnet-two.2 bin/config-wizard
```



## Step 3: Pair your Operator to your Streamr Node <a href="#step-3-pair-your-operator-to-your-streamr-node" id="step-3-pair-your-operator-to-your-streamr-node"></a>
