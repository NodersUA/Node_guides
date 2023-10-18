# Update

```bash
systemctl stop cascadiad
cd /usr/local/bin/ && rm cascadiad
curl -L https://github.com/CascadiaFoundation/cascadia/releases/download/v0.1.7/cascadiad -o cascadiad
chmod +x cascadiad && cd
systemctl restart cascadiad
journalctl -u cascadiad -f --no-hostname -o cat
```
