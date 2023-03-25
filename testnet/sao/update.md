***Update on block height***
```bash
Update height: 
cd $HOME/sao-consensus
git pull
git checkout v...
make build
$HOME/sao-consensus/build/saod version --long | grep -e version -e commit
# 
# commit: 

# After network stopped on current height!!!
systemctl stop saod
mv $HOME/sao-consensus/build/saod $(which saod)
saod version --long | grep -e version -e commit
# 

systemctl restart saod && journalctl -u saod -f -o cat
```

