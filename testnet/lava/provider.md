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
        proxy_pass http://127.0.0.1:2223;
        grpc_pass 127.0.0.1:2223;
    }
}
EOF
```

```bash
sudo systemctl restart nginx
```

```bash
# Create a provider config
LAVA_RPC=$(cat $HOME/.lava/config/config.toml | sed -n '/TCP or UNIX socket address for the RPC server to listen on/{n;p;}' | sed 's/.*://; s/".*//')
LAVA_GRPC=$(cat $HOME/.lava/config/app.toml | sed -n '/Address defines the gRPC server address to bind to/{n;p;}' | sed 's/.*://; s/".*//')
LAVA_API=$(cat $HOME/.lava/config/app.toml | sed -n '/Address defines the API server to listen on./{n;p;}' | sed 's/.*://; s/".*//')

AXELART_RPC=$(cat $HOME/.axelar_testnet/config/config.toml | sed -n '/TCP or UNIX socket address for the RPC server to listen on/{n;p;}' | sed 's/.*://; s/".*//')
AXELART_GRPC=$(cat $HOME/.axelar_testnet/config/app.toml | sed -n '/Address defines the gRPC server address to bind to/{n;p;}' | sed 's/.*://; s/".*//')
AXELART_API=$(cat $HOME/.axelar_testnet/config/app.toml | sed -n '/Address defines the API server to listen on./{n;p;}' | sed 's/.*://; s/".*//')

AXELAR_RPC=$(cat $HOME/.axelar_mainnet/config/config.toml | sed -n '/TCP or UNIX socket address for the RPC server to listen on/{n;p;}' | sed 's/.*://; s/".*//')
AXELAR_GRPC=$(cat $HOME/.axelar_mainnet/config/app.toml | sed -n '/Address defines the gRPC server address to bind to/{n;p;}' | sed 's/.*://; s/".*//')
AXELAR_API=$(cat $HOME/.axelar_mainnet/config/app.toml | sed -n '/Address defines the API server to listen on./{n;p;}' | sed 's/.*://; s/".*//')

echo "LAVA_RPC:"$LAVA_RPC "LAVA_GRPC:"$LAVA_GRPC "LAVA_API:"$LAVA_API
echo "AXELART_RPC:"$AXELART_RPC "AXELART_GRPC:"$AXELART_GRPC "AXELART_API:"$AXELART_API
echo "AXELAR_RPC:"$AXELAR_RPC "AXELAR_GRPC:"$AXELAR_GRPC "AXELAR_API:"$AXELAR_API
```

```bash
mkdir $HOME/config
sudo tee << EOF >/dev/null $HOME/config/lavaprovider.yml
endpoints:
  - api-interface: tendermintrpc
    chain-id: LAV1
    network-address:
      address: 0.0.0.0:2223
      disable-tls: true
    node-urls:
      - url: ws://127.0.0.1:$LAVA_RPC/websocket
      - url: http://127.0.0.1:$LAVA_RPC
  - api-interface: grpc
    chain-id: LAV1
    network-address:
      address: 0.0.0.0:2223
      disable-tls: true
    node-urls:
      url: 127.0.0.1:$LAVA_GRPC
  - api-interface: rest
    chain-id: LAV1
    network-address:
      address: 0.0.0.0:2223
      disable-tls: true
    node-urls:
      url: http://127.0.0.1:$LAVA_API
  - api-interface: tendermintrpc
    chain-id: AXELAR
    network-address:
      address: 0.0.0.0:2223
      disable-tls: true
    node-urls:
      - url: ws://127.0.0.1:$AXELAR_RPC/websocket
      - url: http://127.0.0.1:$AXELAR_RPC
  - api-interface: grpc
    chain-id: AXELAR
    network-address:
      address: 0.0.0.0:2223
      disable-tls: true
    node-urls:
      url: 127.0.0.1:$AXELAR_GRPC
  - api-interface: rest
    chain-id: AXELAR
    network-address:
      address: 0.0.0.0:2223
      disable-tls: true
    node-urls:
      url: http://127.0.0.1:$AXELAR_API
  - api-interface: tendermintrpc
    chain-id: AXELART
    network-address:
      address: 0.0.0.0:2223
      disable-tls: true
    node-urls:
      - url: ws://127.0.0.1:$AXELART_RPC/websocket
      - url: http://127.0.0.1:$AXELART_RPC
  - api-interface: grpc
    chain-id: AXELART
    network-address:
      address: 0.0.0.0:2223
      disable-tls: true
    node-urls:
      url: 127.0.0.1:$AXELART_GRPC
  - api-interface: rest
    chain-id: AXELART
    network-address:
      address: 0.0.0.0:2223
      disable-tls: true
    node-urls:
      url: http://127.0.0.1:$AXELART_API
EOF
```

```bash
# Open ports
ufw allow $LAVA_RPC
ufw allow $LAVA_GRPC
ufw allow $LAVA_API

ufw allow $AXELAR_RPC
ufw allow $AXELAR_GRPC
ufw allow $AXELAR_API

ufw allow $AXELART_RPC
ufw allow $AXELART_GRPC
ufw allow $AXELART_API
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

```bash
# Stake
lavap tx pairing stake-provider LAV1 "1000000000ulava" "$DOMEN:443,2" 2 -y --from wallet --provider-moniker "$MONIKER" --gas-prices 0.1ulava --gas-adjustment 1.5 --gas auto -y
lavap tx pairing stake-provider AXELAR "50000000000ulava" "$DOMEN:443,2" 2 -y --from wallet --provider-moniker "$MONIKER" --gas-prices 0.1ulava --gas-adjustment 1.5 --gas auto -y
lavap tx pairing stake-provider AXELART "50000000000ulava" "$DOMEN:443,2" 2 -y --from wallet --provider-moniker "$MONIKER" --gas-prices 0.1ulava --gas-adjustment 1.5 --gas auto -y
```

```bash
# Check provider
lavap test rpcprovider --from wallet
```
