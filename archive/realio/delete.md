# Delete

```bash
systemctl stop realio-networkd && \
systemctl disable realio-networkd && \
rm /etc/systemd/system/realio-networkd.service && \
systemctl daemon-reload && \
cd $HOME && \
rm -rf .realio-network realio-network && \
rm -rf $(which realio-networkd)
```
