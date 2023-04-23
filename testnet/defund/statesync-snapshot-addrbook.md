# StateSync/Snapshot/AddrBook
**SnapShot**
```bash
sudo systemctl stop defundd

cp $HOME/.defund/data/priv_validator_state.json $HOME/.defund/priv_validator_state.json.backup 

defundd tendermint unsafe-reset-all --home $HOME/.defund --keep-addr-book 
curl https://snapshots2-testnet.nodejumper.io/defund-testnet/orbit-alpha-1_2023-04-23.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.defund

mv $HOME/.defund/priv_validator_state.json.backup $HOME/.defund/data/priv_validator_state.json 

sudo systemctl restart defundd
sudo journalctl -u defundd -f --no-hostname -o cat
```
**If you cannot connect to peers long time download addrbook**

```bash
curl -s https://snapshots2-testnet.nodejumper.io/defund-testnet/addrbook.json > $HOME/.defund/config/addrbook.json

systemctl restart defundd && journalctl -u defundd -f -o cat
```
