# Delete

```bash
sudo systemctl stop penumbra tendermint cometbft df_penumbra
sudo systemctl disable penumbra tendermint cometbft df_penumbra
cd $HOME && sudo rm -rf penumbra .penumbra df_penumbra tendermint cometbft
rm /etc/systemd/system/penumbra.service
rm /etc/systemd/system/tendermint.service
rm /etc/systemd/system/cometbft.service
rm -rf ~/.local/share/pcli/
rm -rf ~/.local/share/penumbra-testnet-archive/
sudo systemctl daemon-reload
rm $(which pcli) && rm $(which pd)
```
