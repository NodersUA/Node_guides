# Cat Protcol

Install developer packages

```bash
sudo apt update && sudo apt-get install ca-certificates jq curl gnupg lsb-release nano -y
```

```bash
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
```

```bash
sudo apt update && sudo apt install yarn -y
```

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
git clone https://github.com/CATProtocol/cat-token-box && cd cat-token-box
```

```bash
sudo chown -R $USER:$USER ~/cat-token-box
```

```bash
sudo yarn install && yarn build
```

```bash
cd packages/tracker && sudo chmod 777 docker/data
sudo docker compose up -d
yarn migration:run
```

```bash
yarn start:worker:prod
```
