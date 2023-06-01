# Update

```bash
sudo systemctl disable fleekd
sudo systemctl stop fleekd
cd $HOME/ursa && git pull origin main
docker build -t ursa -f ./Dockerfile .
docker pull ghcr.io/fleek-network/ursa:latest
docker run -d -p 4069:4069 -p 6009:6009 -v $HOME/.ursa/:/root/.ursa/:rw --name ursa-cli -it ghcr.io/fleek-network/ursa:latest
docker logs ursa-cli -fn100

```
