# Rivalz

```bash
sudo apt update
sudo apt install curl software-properties-common
```

```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
```

```bash
sudo apt install -y nodejs
```

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
```

```bash
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```

```bash
nvm install 20.0.0
```

```bash
nvm use 20.0.0
```

```bash
npm i -g rivalz-node-cli
```

```bash
rivalz run
```

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

```bash
sudo tee /etc/systemd/system/rivalz.service > /dev/null <<EOF
[Unit]
Description=rivalz Service
After=network.target

[Service]
Type=simple
ExecStart=$(which rivalz) run
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF
```

```bash
# Start the node
systemctl daemon-reload
systemctl enable rivalz
systemctl restart rivalz && journalctl -u rivalz -f -o cat
```
