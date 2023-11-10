# Installation

### Install Node <a href="#faucet---testnet-hlg" id="faucet---testnet-hlg"></a>

```bash
# Update the repositories
apt update && apt upgrade -y
```

```bash
# Install NVM on Ubuntu
curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash 
echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.bash_profile
echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> ~/.bash_profile
echo '[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"' >> ~/.bash_profile
source ~/.bash_profile
```

```bash
# Install NodeJS
nvm install v20.8.0
```

```bash
# Install Holograph CLI
npm install -g @holographxyz/cli
```

This command must be run before all other `holograph` commands. The `holograph config` command will walk you through setting the RPC URls and wallet. Simply follow the prompts.

1. `Which networks do you want to operate?` - Choose a network in which you can take test tokens, for example goerli and mumbai
2. `Enter the provider url for fuji. Leave blank to use https://api.avax-test.network/ext/bc/C/rpc:` - press enter or copy and paste an RPC url
3. `Default private key to use when sending all transactions (will be password encrypted)` - Enter the private key for the wallet. Input is hidden for security reasons.
4. `Please enter the password to encrypt the private key with` - Enter some password for your wallet or press `enter` to create a passwordless wallet

```bash
holograph config
```

<figure><img src="../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

Request test tokens from the faucet \[ [goerly](https://goerli-faucet.pk910.de/) | [mumbai](https://faucet.polygon.technology/) ]

### Faucet - Testnet HLG <a href="#faucet---testnet-hlg" id="faucet---testnet-hlg"></a>

To get testnet HLG tokens, you must run the `holograph faucet` command. You are allowed to claim 100 HLG once every 24 hours per blockchain. If you want to operate on multiple blockchains, you will need to call this command on every network.

1. `Would you like to request $HLG tokens?` - Enter `y`
2. `Select the network to request tokens on` - Select a network
3. `The gas cost will be approximately 0.003015846474569148 AVAX. Continue?` - Enter `y`

The CLI will send a transaction and you will receive testnet HLG.

```bash
holograph faucet
```

<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

### Operator <a href="#operating" id="operating"></a>

To become an operator, you must bond HLG into a pod. You are required to maintain uptime or risk having your HLG slashed. Please read our [Operator Network Specification](https://docs.holograph.xyz/about/operator-network-specification) for more information.

To bond into a pod, you must run the `holograph operator:bond` command.

1. `Do you have the operator with the wallet you are bonding from running on the network and are ready to proceed?` - Enter `y`
2. `Select the network to bond to` - Select a network
3. `Enter the pod number to join` - Select a pod number from the list. The number shown is the minimum required to join.
4. `Enter the amount of tokens to deposit (Units in ether)` - Enter how much you want to bond in ETH units
5. `Next steps submit the transaction, would you like to proceed?` - Enter `y`
6. The CLI will submit a transaction to bond you into the pod
7. `Last chance to start your operator if you don't have it running already. Would you like to proceed?` - We suggest running as an operator immediately. Enter `y`

It is very important to keep the CLI running at all times. When jobs are created and you are selected to execute the job, the CLI will submit the required transaction. If the command dies for any reason, you can always restart by running the `holograph operator` command.

```bash
holograph operator:bond
```

<figure><img src="../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>

### Service File <a href="#operating" id="operating"></a>

If you have created a wallet password:

```bash
# Enter your wallet password
pass=<your_password>

sudo tee /etc/default/holographd > /dev/null <<EOF
HOLOGRAPH_PASSWORD=$pass
EOF
```

```bash
sudo tee /etc/systemd/system/holographd.service > /dev/null <<EOF
[Unit]
Description=Holograph
After=network.target
[Service]
Type=simple
User=root
EnvironmentFile=/etc/default/holographd
ExecStart=/bin/bash -c '$(which node) $(which holograph) operator --mode=auto --unsafePassword=\$HOLOGRAPH_PASSWORD --sync'
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

If the wallet was created without a password:

```bash
sudo tee /etc/systemd/system/holographd.service > /dev/null <<EOF
[Unit]
Description=Holograph
After=network.target
[Service]
Type=simple
User=root
ExecStart=/bin/bash -c '$(which node) $(which holograph) operator --mode=auto --sync'
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

Next step...

```bash
systemctl daemon-reload
systemctl enable holographd
systemctl restart holographd
```

```bash
# Check logs
journalctl -u holographd -f -o cat
```
