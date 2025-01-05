# OpenLedger

```bash
sudo apt update && sudo apt install -y xvfb x11-apps tightvncserver libgtk-3-0 libnotify4 libnss3 libxss1 libxtst6 xdg-utils libatspi2.0-0 libsecret-1-0
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
```
```bash
sudo sed -i 's/^#X11UseLocalhost yes/X11UseLocalhost yes/' /etc/ssh/sshd_config

Xtightvnc :1 -desktop X -auth /root/.Xauthority -geometry 1024x768 -depth 24 -rfbwait 120000 -rfbauth /root/.vnc/passwd -rfbport 5901 -fp /usr/share/fonts/X11/misc/,/usr/share/fonts/X11/Type1/,/usr/share/fonts/X11/75dpi/,/usr/share/fonts/X11/100dpi/ -co /etc/X11/rgb &

Xvfb :10 -screen 0 1024x768x16 &
export DISPLAY=localhost:10.0
echo $DISPLAY
```
