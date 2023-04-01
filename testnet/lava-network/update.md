# Update

### Update <a href="#t7co" id="t7co"></a>

```bash
cd $HOME/lava
git fetch --all 
git checkout v...
make install
sudo cp $HOME/go/bin/lavad /usr/local/bin/lavad /.lava/cosmovisor/genesis/bin/lavad
```

Check version 

```bash
lavad version
```

Restart node and check logs

```bash
sudo systemctl restart lavad && sudo journalctl -fu lavad -o cat
```
