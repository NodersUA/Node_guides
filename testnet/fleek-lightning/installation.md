# Installation

### Automatic Installation

```bash
Soon...
```

### Manual Installation

#### **Prepare the server**

```bash
# Add superuser
adduser lgtn
```

```bash
# Add user to sudo group
usermod -aG sudo lgtn
```

```bash
# Switch to the new user by using the command:
su lgtn
```

```bash
cd /home/lgtn
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
git clone -b testnet-alpha-0 https://github.com/fleek-network/lightning.git ~/fleek-network/lightning && \
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

#### Register node

[https://discord.gg/RMMfFVBY](https://discord.gg/RMMfFVBY)

[https://discord.com/channels/965698989464887386/1148299261713338381](https://discord.com/channels/965698989464887386/1148299261713338381)

#### Restart the service:

```shell
sudo systemctl daemon-reload && \
sudo systemctl enable lightning

sudo mkdir -p /var/log/lightning
sudo touch /var/log/lightning/output.log
sudo touch /var/log/lightning/diagnostic.log
sudo systemctl start lightning.service
```

```bash
# Check logs
tail -fn100 /var/log/lightning/output.log
tail -fn100 /var/log/lightning/diagnostic.log
```
