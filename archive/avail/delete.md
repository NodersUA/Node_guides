# Delete

Delete avail

```bash
systemctl stop availd
systemctl disable availd
rm /usr/local/bin/avail
rm /etc/systemd/system/availd.service
rm -rf avail .avail
```

Delete avail-light

```
systemctl stop avail_light
systemctl disable avail_light
rm /usr/local/bin/avail_light
rm /etc/systemd/system/avail_light.service
rm -rf avail-light .avail-light
```
