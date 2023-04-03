## Automatic Installation
```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/fleek)
```

## Manual Installation

1. Update, upgrade and install requirements:
```shell
sudo apt-get update && \
sudo apt-get upgrade -y && \
sudo curl https://sh.rustup.rs -sSf | sh -s -- -y && \
source "$HOME/.cargo/env" && \
cargo install sccache && \
sudo apt-get install build-essential cmake clang pkg-config libssl-dev protobuf-compiler -y
```
2. Clone repository and build:
```shell
git clone https://github.com/fleek-network/ursa && \
cd ursa && \
make install
```
3. Create a service:
```shell

sudo tee /etc/systemd/system/fleekd.service > /dev/null <<EOF
[Unit]
Description=Fleek Node
After=network.target
[Service]
User=$USER
Type=simple
ExecStart=$(which ursa)
Restart=on-failure
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```
4. Restart the service:
```shell
sudo systemctl daemon-reload && \
sudo systemctl enable fleekd && \
sudo systemctl restart fleekd && \
sudo journalctl -u fleekd -f -o cat
```
5. Set an identity:
```shell
IDENTITY="nickname"
```
6. Replace default identity:
```shell
systemctl stop fleekd && \
sed -i.bak -e "s/^identity *=.*/identity = \"${IDENTITY}\"/" $HOME/.ursa/config.toml && \
rm $HOME/.ursa/keystore/*; \
sudo systemctl restart fleekd && \
sudo journalctl -u fleekd -f -o cat
```

7. Backup:
```shell
echo -e "$HOME/.ursa/keystore/"
```
