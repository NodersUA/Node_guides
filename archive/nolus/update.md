***Udate on block hight***
```bash
Update height:
cd $HOME/nolus-core
git pull
git checkout v...
make build
$HOME/nolus-core/target/release/nolusd version --long | grep -e version -e commit -e build -e commit
#version:v...
#commit:...

# After network stopped on current block!!!
systemctl stop nolusd
mv $HOME/nolus-core/target/release/nolusd $(which nolusd)
nolusd version --long | grep -e version -e commit
# 

systemctl restart nolusd && journalctl -u nolusd -f -o cat
```
