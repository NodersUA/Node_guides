***Update on block height***
```bash
Update height: 
cd $HOME/nibiru
git pull
git checkout v...
make build
$HOME/nibiru/build/nibid version --long | grep -e version -e commit
# v...
# commit:...

# After network stopped on current block!!!
systemctl stop nibid
mv $HOME/nibiru/build/nibid $(which nibid)
nibid version --long | grep -e version -e commit
# 

systemctl restart nibid && journalctl -u nibid -f -o cat
```
