***Update on block height***
```bash
cd $HOME/sao-consensus
git pull
git checkout v...
make build
$HOME/sao-consensus/build/saod version --long | grep -e version -e commit
# 
# commit: 

# After network stop on current block!!!
systemctl stop saod
mv $HOME/sao-consensus/build/saod $(which saod)
saod version --long | grep -e version -e commit
# 

systemctl restart saod && journalctl -u saod -f -o cat
```
