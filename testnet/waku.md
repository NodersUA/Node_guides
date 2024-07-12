# Waku

## _**Automatic Installation**_

```bash
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/waku)
```

## **Manual Installation**

### Node

Update the repositories

```bash
sudo apt update && sudo apt upgrade -y
```

#### Install docker <a href="#install-docker" id="install-docker"></a>

```bash
# Install Docker
if ! [ -x "$(command -v docker)" ]; then
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo usermod -aG docker $USER
docker --version
else
echo $(docker --version)
fi
```

```bash
# Install Docker Compose
if ! [ -x "$(command -v docker-compose)" ]; then
sudo curl -L "https://github.com/docker/compose/releases/download/v2.2.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose 
sudo chmod +x /usr/local/bin/docker-compose 
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
docker-compose --version
else
echo $(docker-compose --version)
fi
```

#### Install node <a href="#install-docker" id="install-docker"></a>

```bash
git clone https://github.com/waku-org/nwaku-compose
cd nwaku-compose
```

```
// Some code
```

```bash
cp .env.example .env

```
