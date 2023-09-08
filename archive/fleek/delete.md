# Delete

```bash
sudo systemctl stop fleekd && \
sudo systemctl disable fleekd && \
rm /etc/systemd/system/fleekd.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf ursa .ursa && \
rm $(which fleekd)
```
