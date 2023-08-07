# Update

```bash
systemctl stop cascadiad
cd /usr/local/bin/
curl -L https://github.com/CascadiaFoundation/cascadia/releases/download/v0.1.4/cascadiad -o cascadiad
chmod +x cascadiad && cd
systemctl restart cascadiad
journalctl -u cascadiad -f --no-hostname -o cat
```
