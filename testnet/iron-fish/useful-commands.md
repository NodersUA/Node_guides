***Useful commands***
```bash
# Check balance
ironfish wallet:balance $IRONFISH_WALLET
```
```bash
# Get tokens from faucet
ironfish faucet
```
```bash
# Check status
ironfish status -f
```
```bash
# Chech logs of the node
journalctl -u ironfishd -f
```
```bash
# Check logs of miner
journalctl -u ironfishd-miner -f
```
```bash
# Start the node
sudo service ironfishd stop
```
```bash
# Stop mining
sudo service ironfishd-miner stop
```
```bash
# Restart node
sudo service ironfishd restart
```
```bash
# Restart miner
sudo service ironfishd-miner restart
```
```bash
# Restart cron
sudo systemctl restart cron
```
```bash
# Check logs of cron
tail -F $HOME/logfile.log
```
```bash
# Check the crown logs for weekly tasks
cat $HOME/bms.log
```
***Change the number of threads***
```bash
sudo service ironfishd-miner stop
sudo nano /etc/systemd/system/ironfishd-miner.service
Crtr + o - save, enter, Ctrl + x - exit
systemctl daemon-reload
systemctl enable ironfishd-miner.service
systemctl start ironfishd-miner.service
```
