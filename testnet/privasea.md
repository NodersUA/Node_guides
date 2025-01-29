# Privasea

### Automatic Installation

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/privasea)
```

### Node

```bash
sudo apt update
```

```bash
# Install docker
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/system/docker)
```

```bash
docker pull privasea/acceleration-node-beta
```

```bash
docker run -it -v "/privasea/config:/app/config"  \
privasea/acceleration-node-beta:latest ./node-calc new_keystore
```

```bash
cd /privasea/config
mv * wallet_keystore
```

```bash
docker run -d --name privasea \
  -v "/privasea/config:/app/config" \
  -e KEYSTORE_PASSWORD=my_pass \
  privasea/acceleration-node-beta:latest
```
