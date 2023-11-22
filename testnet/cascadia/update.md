# Update

```bash
cd ~/.cascadiad/config
sed -i "s/^timeout_propose =.*/timeout_propose = \"2.7s\"/" config.toml
sed -i "s/^timeout_prevote =.*/timeout_prevote = \"0.9s\"/" config.toml
sed -i "s/^timeout_precommit =.*/timeout_precommit = \"0.9s\"/" config.toml
sed -i "s/^timeout_commit =.*/timeout_commit = \"3.6s\"/" config.toml
```

```bash
systemctl stop cascadiad
cd /usr/local/bin/ && rm cascadiad
curl -L https://github.com/CascadiaFoundation/cascadia/releases/download/v0.1.7/cascadiad -o cascadiad
chmod +x cascadiad && cd
systemctl restart cascadiad
journalctl -u cascadiad -f --no-hostname -o cat
```
