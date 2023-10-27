# Provider

```bash
# Install nginx
sudo apt install nginx
```

```bash
# Install certbot 
sudo apt install certbot net-tools nginx python3-certbot-nginx -y
```

```bash
# Set the variables

# Come up with the name of your node and replace it instead <your_moniker>
DOMEN="<your_domen>"

echo 'export DOMEN='$DOMEN>> $HOME/.bash_profile
source $HOME/.bash_profile
# check whether the last command was executed
```

```bash
# Request a certificate
sudo certbot certonly --nginx -d $DOMEN -d $DOMEN
```

```bash
# Configuring nginx:
tee /etc/nginx/conf.d/lava.conf > /dev/null << EOF
server {
listen 443 ssl http2;
server_name $DOMEN;

    ssl_certificate "/etc/letsencrypt/live/$DOMEN/fullchain.pem";
    ssl_certificate_key "/etc/letsencrypt/live/$DOMEN/privkey.pem";
    error_log /var/log/nginx/debug.log debug;

    location / {
        proxy_pass http://$(wget -qO- eth0.me):2223;
        grpc_pass $(wget -qO- eth0.me):2223;
    }
}
EOF
```

```bash
sudo systemctl restart nginx
```

```bash
# Create a provider config
RPC=$(cat $HOME/.lava/config/config.toml | sed -n '/TCP or UNIX socket address for the RPC server to listen on/{n;p;}' | sed 's/.*://; s/".*//')
GRPC=$(cat $HOME/.lava/config/app.toml | sed -n '/Address defines the gRPC server address to bind to/{n;p;}' | sed 's/.*://; s/".*//')
API=$(cat $HOME/.lava/config/app.toml | sed -n '/Address defines the API server to listen on./{n;p;}' | sed 's/.*://; s/".*//')

echo "RPC:"$RPC "GRPC:"$GRPC "API:"$API
```

```bash
mkdir $HOME/config
sudo tee << EOF >/dev/null $HOME/config/lavaprovider.yml
endpoints:
  - api-interface: tendermintrpc
    chain-id: LAV1
    network-address:
      address: 0.0.0.0:2224
      disable-tls: true
    node-urls:
      - url: ws://127.0.0.1:$RPC/websocket
      - url: http://127.0.0.1:$RPC
  - api-interface: grpc
    chain-id: LAV1
    network-address:
      address: 0.0.0.0:2224
      disable-tls: true
    node-urls:
      url: 127.0.0.1:$GRPC
  - api-interface: rest
    chain-id: LAV1
    network-address:
      address: 0.0.0.0:2224
      disable-tls: true
    node-urls:
      url: http://127.0.0.1:$API
EOF
```

```bash
# Open ports
ufw allow $RPC
ufw allow $GRPC
ufw allow $API
```

```bash
# Create a service
tee /etc/systemd/system/lavap.service > /dev/null << EOF
[Unit]
Description=Lava provider
After=network-online.target

[Service]
User=$USER
WorkingDirectory=$HOME/config
ExecStart=$(which lavap) rpcprovider lavaprovider.yml --geolocation 2 --from wallet --chain-id lava-testnet-2 --keyring-backend test
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

```bash
# Start service
sudo systemctl daemon-reload
systemctl enable lavap
sudo systemctl restart lavap && sudo journalctl -u lavap -f --no-hostname -o cat
```
