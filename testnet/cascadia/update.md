# Update

```bash
systemctl stop cascadiad
cd /usr/local/bin/
curl -L https://github.com/CascadiaFoundation/cascadia/releases/download/v0.1.3/cascadiad-v0.1.3-linux-amd64 -o cascadiad
chmod +x cascadiad && cd
systemctl restart cascadiad
journalctl -u cascadiad -f --no-hostname -o cat
```
