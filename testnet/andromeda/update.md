***Update on block height***
```bash
cd $HOME/andromedad
git pull
git checkout v...
make build
$HOME/andromedad/build/andromedad version --long | grep -e version -e commit
# 
# commit: 

# After stopping network on block!!!
systemctl stop andromedad
mv $HOME/andromedad/build/andromedad $(which andromedad)
andromedad version --long | grep -e version -e commit
# 

systemctl restart andromedad && journalctl -u andromedad -f -o cat
```

