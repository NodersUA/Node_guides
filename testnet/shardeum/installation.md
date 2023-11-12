# Installation

### Automatic Installation

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/shardeum)
```

### Node

Update the repositories

```bash
sudo apt update && sudo apt upgrade -y
```

Install developer packages

```bash
apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev chrony -y
```

#### Install docker <a href="#install-docker" id="install-docker"></a>

```bash
sudo apt install docker.io 
```

**Check version**

```bash
docker --version
```

Check the version should issue 20.10.12 or higher

Install docker-compose

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose && docker-compose --version
```

Checking the version should give 1.29.2 or higher

It will give an error that there is no access, give access to the command below and perform the installation again

Setup permissions for docker-compose:

```bash
sudo chmod +x /usr/local/bin/docker-compose
```

Install apparmor

```bash
sudo apt install apparmor-profiles
```

### Installing and enabling the validator <a href="#hn8m" id="hn8m"></a>

```bash
curl -O https://gitlab.com/shardeum/validator/dashboard/-/raw/main/installer.sh && chmod +x installer.sh && ./installer.sh
```

**The terminal will ask questions about your setup settings.**

_<mark style="color:green;">**Give permission to collect validator data for bug reporting:**</mark>_

<mark style="color:red;">**By running this installer, you agree to allow the Shardeum team to collect this data. (y/n)?:**</mark>

_<mark style="color:green;">**Enter y to setup the web based dashboard:**</mark>_

<mark style="color:red;">**Do you want to run the web based Dashboard? (y/n): y**</mark>

_<mark style="color:green;">**Set a password for dashboard access:**</mark>_

<mark style="color:red;">**Set the password to access the Dashboard:**</mark>

_<mark style="color:green;">**Add a custom session port for the web based dashboard or hit enter for port 8080:**</mark>_

<mark style="color:green;">**Enter the port (1025-65536) to access the web based Dashboard (default 8080):**</mark>

<mark style="color:red;">For the default port, just Enter, if you want a custom one, select and enter it (for example, 3101)</mark>

<mark style="color:green;">**This allows p2p communication between nodes. Enter the first port (1025-65536) for p2p communication (default 9001):**</mark>

<mark style="color:red;">For the default port, just Enter, if you want a custom one, select and enter it (for example, 9113)</mark>

<mark style="color:green;">**Enter the second port (1025-65536) for p2p communication (default 10001):**</mark>

<mark style="color:red;">For the default port, just Enter, if you want a custom one, select and enter it (for example, 10013)</mark>

_**Add a custom path or install to root:**_

_<mark style="color:green;">**What base directory should the node use (defaults to \~/.shardeum):**</mark>_

Waiting for the installation to finish from about 10 to 30 minutes.

### Running cli and validator <a href="#4ure" id="4ure"></a>

Go to folder .shardeum

```bash
cd && cd .shardeum
```

Launching a shell

```bash
./shell.sh
```

Launching gui

```bash
operator-cli gui start
```

Check status of the node

```bash
operator-cli gui status
```

The output should look something like this

<figure><img src="https://img2.teletype.in/files/d1/23/d123aec3-ee0d-42c7-ada4-0b2c1e2d45fb.jpeg" alt=""><figcaption></figcaption></figure>

<figure><img src="https://img2.teletype.in/files/d1/23/d123aec3-ee0d-42c7-ada4-0b2c1e2d45fb.jpeg" alt=""><figcaption></figcaption></figure>

Go to the browser using the link: https://\<your\_IP>:8080 and enter your password

At this stage, an error may occur when entering the correct password, it is not possible to enter the account. In this case, enter the command

```bash
 operator-cli gui set password <your_password>
```

There will be general information on the validator

<figure><img src="https://img3.teletype.in/files/e1/bd/e1bd174a-593c-4aa0-8f28-53e43bb4e3a2.png" alt=""><figcaption></figcaption></figure>

<figure><img src="https://img3.teletype.in/files/e1/bd/e1bd174a-593c-4aa0-8f28-53e43bb4e3a2.png" alt=""><figcaption></figcaption></figure>

Run the validator Go to the "Maintenance" tab and press the "Start Node" button

<figure><img src="https://img1.teletype.in/files/88/68/88680203-a3cc-4023-a30b-1e11e97b4aad.png" alt=""><figcaption></figcaption></figure>

<figure><img src="https://img1.teletype.in/files/88/68/88680203-a3cc-4023-a30b-1e11e97b4aad.png" alt=""><figcaption></figcaption></figure>

Go to terminal and start node

```bash
operator-cli start
```

We go back to the browser, wait a couple of minutes and refresh the page. If the button changed to "Stop Node", then you did everything right and the node is running.

<figure><img src="https://img1.teletype.in/files/ca/8e/ca8ec5e6-a361-4bed-b131-2cdc0cd97fc3.png" alt=""><figcaption></figcaption></figure>

<figure><img src="https://img1.teletype.in/files/ca/8e/ca8ec5e6-a361-4bed-b131-2cdc0cd97fc3.png" alt=""><figcaption></figcaption></figure>

### Validator[ ](https://docs.shardeum.org/node/run/validator#step-6-monitor-validator)Monitoring[​](https://docs.shardeum.org/node/run/validator#step-6-monitor-validator) <a href="#hssp" id="hssp"></a>

Go to "Performance" to see the hardware performance of your node here.

To get more detailed information about the state of your node, run the following in the CLI:

```bash
operator-cli status
```

<figure><img src="https://img2.teletype.in/files/19/62/1962b329-bea3-4741-ae57-a9e723afe033.jpeg" alt=""><figcaption></figcaption></figure>

<figure><img src="https://img2.teletype.in/files/19/62/1962b329-bea3-4741-ae57-a9e723afe033.jpeg" alt=""><figcaption></figcaption></figure>

#### Connecting the wallet and stake tokens to the validator <a href="#sutg" id="sutg"></a>

Go to [website](https://docs.shardeum.org/network/endpoints) and add network Sphinx 1.X in MetaMask.

<figure><img src="https://img1.teletype.in/files/87/20/87204cfb-9572-4af6-9bf3-ec6db0a07adb.png" alt=""><figcaption></figcaption></figure>

<figure><img src="https://img1.teletype.in/files/87/20/87204cfb-9572-4af6-9bf3-ec6db0a07adb.png" alt=""><figcaption></figcaption></figure>

Go into [faucet branch](https://faucet-sphinx.shardeum.org/), make a post on twitter with your ethereum address, copy the link to the post, paste the copied link into the faucet and get test tokens in the Sphinx network (Betanet)

Also now you can get tokens in the Liberty network, they are not needed for the current testnet, but it will not be superfluous

* **Liberty 1.X:** [https://faucet.liberty10.shardeum.org/](https://faucet.liberty10.shardeum.org/)
* **Liberty 2.X:** [https://faucet.liberty20.shardeum.org/](https://faucet.liberty20.shardeum.org/)

Go to your validator page and connect the Metamask wallet (Shardeum Sphinx 1.X network)

<figure><img src="https://img3.teletype.in/files/6a/c9/6ac99c40-355a-406d-bb01-f9385ddd923a.png" alt=""><figcaption></figcaption></figure>

<figure><img src="https://img3.teletype.in/files/6a/c9/6ac99c40-355a-406d-bb01-f9385ddd923a.png" alt=""><figcaption></figcaption></figure>

### Additional faucets for tokens <a href="#29st" id="29st"></a>

**Discord (once in 12 hours)**

1. Sphinx [https://discord.com/channels/933959587462254612/1070780355931541514](https://discord.com/channels/933959587462254612/1070780355931541514)
2. [Liberty](https://discord.com/channels/933959587462254612/1070780355931541514Liberty) 2.X:[https://discord.com/channels/933959587462254612/1031497272191627284](https://discord.com/channels/933959587462254612/1031497272191627284)
3. [Liberty](https://discord.com/channels/933959587462254612/1031497272191627284Liberty) 1.X: [https://discord.com/channels/933959587462254612/1021737152251441244](https://discord.com/channels/933959587462254612/1021737152251441244)

**Chaindrop Faucet Website (once in 30 min)**

Sphinx 1.X:

[https://chaindrop.org/?chainid=8082\&token=0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee](https://chaindrop.org/?chainid=8082\&token=0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee)

Liberty 2.X:

[https://chaindrop.org/?chainid=8081\&token=0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee](https://chaindrop.org/?chainid=8081\&token=0xeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee)

Press the "Add Stake" button, enter the amount of the stake (leaving a little for the commission. The minimum stake is 10 SHM, the faucet gives out 15 SHM, 14 of which I staked)

<figure><img src="https://img3.teletype.in/files/a9/75/a97522e6-ce7d-489c-95c1-7c4d8ece4bfb.png" alt=""><figcaption></figcaption></figure>

<figure><img src="https://img3.teletype.in/files/a9/75/a97522e6-ce7d-489c-95c1-7c4d8ece4bfb.png" alt=""><figcaption></figcaption></figure>

<figure><img src="https://img2.teletype.in/files/d4/2a/d42a5de5-cec5-4468-acee-1f9d72299bed.png" alt=""><figcaption></figcaption></figure>

<figure><img src="https://img2.teletype.in/files/d4/2a/d42a5de5-cec5-4468-acee-1f9d72299bed.png" alt=""><figcaption></figcaption></figure>

It remains only to confirm the transaction and the validator is ready to go

<figure><img src="https://img2.teletype.in/files/17/7b/177b3345-da73-451d-b76a-f6ae0eb889e0.png" alt=""><figcaption></figcaption></figure>

<figure><img src="https://img2.teletype.in/files/17/7b/177b3345-da73-451d-b76a-f6ae0eb889e0.png" alt=""><figcaption></figcaption></figure>

###
