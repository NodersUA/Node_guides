***Updates***
```bash
nvm use 18.13

sudo systemctl stop ironfishd

sudo npm update -g ironfish

ironfish migrations:start

sudo systemctl restart ironfishd

# v...
# Check the logs
sudo journalctl -f -u ironfishd

# Exit from logs ctrl+c
```
```bash
# Check status
ironfish status -f
```
