# StateSync/Snapshot/AddrBook

If you cannot connect to peers long time download addrbook

```bash
wget -O $HOME/.defund/config/addrbook.json "https://share.utsa.tech/defund/addrbook.json"

systemctl restart defundd && journalctl -u defundd -f -o cat
```
