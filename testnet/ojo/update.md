***Update on block hight ***
```bash
cd $HOME/ojo
git pull
git checkout v
make build
$HOME/ojo/build/ojod version --long | grep -e version -e commit
# v
# commit: 

# After network stop on current block!!!
systemctl stop ojod
cp $HOME/ojo/build/ojod $(which ojod)
ojod version --long | grep -e version -e commit
```
