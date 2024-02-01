# Delete

```bash
sudo systemctl stop namadad
sudo systemctl disable namadad
rm -rf namada ~/.local/share/namada/
rm /usr/local/bin/namada*
rm /etc/systemd/system/namadad.service
sudo systemctl daemon-reload
```
