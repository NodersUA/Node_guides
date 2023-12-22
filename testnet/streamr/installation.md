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

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

```bash
# Run node
sudo docker run -p 32200:32200 --name streamr --restart unless-stopped -d -v $(cd ~/.streamrDocker && pwd):/home/streamr/.streamr streamr/broker-node:v100.0.0-testnet-two.2
```

```bash
# Check logs
sudo docker logs --follow streamr
```

```bash
# Restart
sudo docker restart streamr
```



## Step 3: Pair your Operator to your Streamr Node <a href="#step-3-pair-your-operator-to-your-streamr-node" id="step-3-pair-your-operator-to-your-streamr-node"></a>

1. Visit your [Operator page](https://streamr.network/hub/network/operators) and find the "Operator's node addresses"
2.  Click the _**Add node address**_ button, paste in your **node address** (the Ethereum public address, not its private key!), click the button in the dialog and remember to click the _**Save**_ button.\


    <figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

    <figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>


3. Ensure sufficient MATIC wallet balance, it is required to pay fees
4.  #### Fund your Operator <a href="#step-5-fund-your-operator" id="step-5-fund-your-operator"></a>

    <figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

    <figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

    <figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>


5.  #### Join sponsorships <a href="#step-7-join-sponsorships" id="step-7-join-sponsorships"></a>

    Visit the [Sponsorships page](https://mumbai.streamr.network/hub/network/sponsorships?tab=all) and find a Sponsorship you want your Operator to start working on. Click the **"Join as Operator"** button and select your stake. Note there is a minimum stake of 5000 `DATA` tokens for each Sponsorship that you join. Joining Sponsorships may lock your tokens for a period of time, defined in the Sponsorship contract.
