# OpenLedger

```bash
sudo apt update && sudo apt install -y xvfb libgtk-3-0 libnotify4 libnss3 libxss1 libxtst6 xdg-utils libatspi2.0-0 libsecret-1-0
```

```bash
# Install docker
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/system/docker)
```

```bash
wget https://cdn.openledger.xyz/openledger-node-1.0.0-linux.zip
unzip openledger-node-1.0.0-linux.zip
sudo dpkg -i openledger-node-1.0.0.deb
sudo apt-get install -f -y
```

```bash
sudo apt-get install desktop-file-utils
sudo dpkg --configure -a
screen -S openledger_node
sudo apt-get install libgbm1 libasound2
openledger-node --no-sandbox
```
