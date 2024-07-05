# Install

## _**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/aleo)
```

## **Manual Installation**

### Setting up a Aleo node

```bash
apt update && apt install make clang pkg-config libssl-dev build-essential gcc xz-utils git curl vim tmux ntp jq llvm ufw -y
```

```bash
git clone --branch mainnet --single-branch https://github.com/AleoNet/snarkOS.git
cd snarkOS && git checkout tags/testnet-beta
./build_ubuntu.sh
```

```bash
cargo install --locked --path .
```

```bash
ufw allow 4130/tcp
ufw allow 3030/tcp
sudo ufw limit out proto tcp to any port 10333
```

```bash
mkdir $HOME/aleo
date >> $HOME/aleo/account_new.txt
snarkos account new >>$HOME/aleo/account_new.txt
sleep 2
cat $HOME/aleo/account_new.txt
echo -e "\033[41m\033[30mPLEASE REMEMBER TO SAVE THE ACCOUNT PRIVATE KEY AND VIEW KEY.\033[0m\n"
sleep 3
mkdir -p /var/aleo/
cat $HOME/aleo/account_new.txt >>/var/aleo/account_backup.txt
echo 'export PROVER_PRIVATE_KEY'=$(grep "Private Key" $HOME/aleo/account_new.txt | awk '{print $3}') >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
sudo tee /etc/systemd/system/aleo_client.service > /dev/null <<EOF
[Unit]
Description=Aleo Client Node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/snarkOS
ExecStart=$(which cargo) run --release -- start --nodisplay --client --network 1
Restart=always
RestartSec=10
LimitNOFILE=10000
[Install]
WantedBy=multi-user.target
EOF
```

```bash
systemctl daemon-reload
systemctl enable aleo_client
systemctl restart aleo_client && journalctl -u aleo_client -f -o cat
```

```bash
PROVER_PRIVATE_KEY=$(grep "Private Key" $HOME/aleo/account_new.txt | awk '{print $3}')
```

```bash
sudo tee /etc/systemd/system/aleo_prover.service > /dev/null <<EOF
[Unit]
Description=SnarkOS Service
After=network.target

[Service]
Type=simple
User=$USER
WorkingDirectory=$HOME/snarkOS
ExecStart=$(which cargo) run --release -- start --prover --nodisplay --network 1 --private-key ${PROVER_PRIVATE_KEY}
Restart=on-failure

[Install]
WantedBy=multi-user.target

EOF
```

```bash
systemctl daemon-reload
systemctl enable aleo_prover.service
systemctl restart aleo_prover.service && journalctl -u aleo_prover -f -o cat
```

```bash
cd $HOME
wget -q -O $HOME/aleo_updater_WIP.sh https://api.nodes.guru/aleo3_updater_WIP.sh && chmod +x $HOME/aleo_updater_WIP.sh

sudo tee /etc/systemd/system/aleo_updater.service > /dev/null <<EOF
[Unit]
Description=Aleo Updater
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/snarkOS
ExecStart=/bin/bash $HOME/aleo_updater_WIP.sh
Restart=always
RestartSec=10
LimitNOFILE=10000
[Install]
WantedBy=multi-user.target
EOF
```

```bash
systemctl daemon-reload
systemctl enable aleo_updater.service
systemctl restart aleo_updater.service && journalctl -u aleo_updater -f -o cat
```
