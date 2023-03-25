***UPD on the block hight *** 

```bash
cd $HOME/dymension
git pull
git checkout 
make build
$HOME/dymension/build/dymd version --long | grep -e version -e commit
# 
# commit: 

# After network will stop on current block!!!
systemctl stop dymd
mv $HOME/dymension/build/dymd $(which dymd)
dymd version --long | grep -e version -e commit
# 

systemctl restart dymd && journalctl -u dymd -f -o cat
```
