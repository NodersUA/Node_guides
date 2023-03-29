# Installation (Docker)

```bash
# Update the repositories
sudo apt update && sudo apt upgrade -y
```

```bash
# Install developer packages
sudo apt install wget jq bc build-essential ufw -y
```

```bash
# Install Docker
. <(wget -qO- https://raw.githubusercontent.com/SecorD0/utils/main/installers/docker.sh)

```

```bash
# Download Genesis
mkdir -p $HOME/.sui && \
wget -qO $HOME/.sui/genesis.blob https://github.com/MystenLabs/sui-genesis/raw/main/devnet/genesis.blob
```

```bash
# Download config
wget -qO $HOME/.sui/fullnode.yaml https://github.com/MystenLabs/sui/raw/main/crates/sui-config/data/fullnode-template.yaml
```

```bash
# Edit config
sed -i -e "s%db-path:.*%db-path: \"$HOME/.sui/db\"%; "\
"s%metrics-address:.*%metrics-address: \"0.0.0.0:9184\"%; "\
"s%json-rpc-address:.*%json-rpc-address: \"0.0.0.0:9000\"%; "\
"s%genesis-file-location:.*%genesis-file-location: \"$HOME/.sui/genesis.blob\"%; " $HOME/.sui/fullnode.yaml

```

```bash
# Open ports
. <(wget -qO- https://raw.githubusercontent.com/SecorD0/utils/main/miscellaneous/ports_opening.sh) \
9000 9184
```

```bash
# Run containe
docker run -dit --name sui_node --restart always -u 0:0 \
  --log-opt max-size=50m --log-opt max-file=3 \
  --network host -v $HOME/.sui:/root/.sui secord/sui \
  --config-path $HOME/.sui/fullnode.yaml
```

```bash
# Check logs
docker logs sui_node -fn100
```
