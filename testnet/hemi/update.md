# Update

```bash
systemctl stop popmd
mv /root/heminetwork/hemi/popm-address.json /root/heminetwork/popm-address.json
cd heminetwork
rm -rf hemi*
curl -L -O https://github.com/hemilabs/heminetwork/releases/download/v0.8.0/heminetwork_v0.8.0_linux_amd64.tar.gz
mkdir -p hemi
tar --strip-components=1 -xzvf heminetwork_v0.8.0_linux_amd64.tar.gz -C hemi
rm heminetwork_v0.8.0_linux_amd64.tar.gz
systemctl restart popmd && journalctl -u popmd -f -o cat
```
