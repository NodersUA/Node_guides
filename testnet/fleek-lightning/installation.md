# Installation

#### **Prepare the server**

```bash
# Add superuser
adduser lgtn
```

```bash
# Add user to sudo group
usermod -aG sudo lgtn

# Switch to the new user by using the command:
su lgtn
cd /home/lgtn
```

### Automatic Installation

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/fleek-lightning)
```

After staking, start the node:

```shell
sudo systemctl daemon-reload && \
sudo systemctl enable lightning

sudo mkdir -p /var/log/lightning
sudo touch /var/log/lightning/output.log
sudo touch /var/log/lightning/diagnostic.log
sudo systemctl restart lightning.service
```

```bash
# Check logs
tail -fn100 /var/log/lightning/output.log
tail -fn100 /var/log/lightning/diagnostic.log
```

### Manual Installation

#### Open ports:

```bash
# Open ports
sudo ufw allow 4200:4299/tcp
sudo ufw allow 4300:4399/udp
```

#### Update, upgrade and install requirements:

```shell
sudo apt-get update && \
sudo apt-get upgrade -y && \
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
# press 1) Proceed with installation (default)

source "$HOME/.cargo/env"

rustup default stable-x86_64-unknown-linux-gnu && \
sudo apt-get install build-essential -y && \
sudo apt-get install cmake clang pkg-config libssl-dev gcc-multilib -y && \
sudo apt-get install protobuf-compiler

```

#### Clone repository and build:

```shell
git clone -b testnet-alpha-1 https://github.com/fleek-network/lightning.git ~/fleek-network/lightning && \
cd ~/fleek-network/lightning && \
cargo clean && cargo update && cargo build && \
sudo ln -s ~/fleek-network/lightning/target/debug/lightning-node /usr/local/bin/lgtn
```

#### Key generator

```bash
lgtn keys generate
```

Backup your keys from \`/home/lgtn/.lightning/keystore/\`

#### Config.toml

```bash
sed -i "s|~/.lightning|/home/lgtn/.lightning|g" "/home/lgtn/.lightning/config.toml"
```

#### Create a service:

```shell
sudo tee /etc/systemd/system/lightning.service > /dev/null <<EOF
[Unit]
Description=Fleek Network Node lightning service

[Service]
User=lgtn
Type=simple
MemoryHigh=32G
RestartSec=15s
Restart=always
ExecStart=lgtn -c /home/lgtn/.lightning/config.toml run
StandardOutput=append:/var/log/lightning/output.log
StandardError=append:/var/log/lightning/diagnostic.log
Environment=TMPDIR=/var/tmp

[Install]
WantedBy=multi-user.target
EOF
```

```bash
sudo chmod 644 /etc/systemd/system/lightning.service
```

#### Set up the Metamask browser extension and Stake <a href="#2-set-up-the-metamask-browser-extension" id="2-set-up-the-metamask-browser-extension"></a>

Open the Metamask `settings`, located in the drop-down (top-right menu options). Set the following property values:

* Network Name: `Fleek Network Testnet`
* RPC URL: `https://rpc.testnet.fleek.network/rpc/v0`
* Chain ID: `59330`
* Currency symbol: `tFLK`

Before proceeding, make sure to have the Fleek Network selected as the metamask network. Once confirmed, visit the [Faucet website](https://faucet.testnet.fleek.network/)

In the [Faucet website](https://faucet.testnet.fleek.network/), you have to click the `Connect Wallet`

Once `Connect Wallet` is ready, proceed to `Mint tFLK` and wait until the balance of the account in your Metamask increases. You need to have `tFLK` before proceeding. Be patient.

Once `tFLK` balance is available, click in the `Stake` button. You'll be required to provided the following details from your node:

* Node Public Key
* Consensus Public Key
* Server IP Address

You can get the details quickly by running the **node details** script in the terminal connected to your machine or server where the node is set up and running, as follows:

```bash
curl https://get.fleek.network/node_details | bash
```

#### Restart the service:

```shell
sudo systemctl daemon-reload && \
sudo systemctl enable lightning

sudo mkdir -p /var/log/lightning
sudo touch /var/log/lightning/output.log
sudo touch /var/log/lightning/diagnostic.log
sudo systemctl restart lightning.service
```

```bash
# Check logs
tail -fn100 /var/log/lightning/output.log
tail -fn100 /var/log/lightning/diagnostic.log
```
