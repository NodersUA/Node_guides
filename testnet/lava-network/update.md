# Update

### Update <a href="#t7co" id="t7co"></a>

```bash
cd $HOME/lava
git fetch --all 
git checkout v0.6.0
make install
sudo mv $HOME/go/bin/lavad /usr/local/bin/lavad
```

Check version 0.6.0

```bash
lavad version
```

Restart node and check logs

```bash
sudo systemctl restart lavad && sudo journalctl -fu lavad -o cat
```
