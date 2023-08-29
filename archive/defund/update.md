# Update

```bash
# Оновлення 
cd && rm -r defund
git clone https://github.com/defund-labs/defund && cd defund
git checkout v...
make build
$HOME/defund/build/defundd version --long | grep -e version -e commit
systemctl stop defundd
mv $HOME/defund/build/defundd $(which defundd)
defundd version --long | grep -e version -e commit
# v...
# commit: ...

systemctl restart defundd && journalctl -u defundd -f -o cat
```
