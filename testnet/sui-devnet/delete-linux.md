# Delete (Linux)

```bash
systemctl stop suid && \
systemctl disable suid && \
rm -rf $HOME/{sui,.sui} /usr/bin/{sui,sui-node,sui-faucet} \
/etc/systemd/system/suid.service && \
systemctl daemon-reload
```
