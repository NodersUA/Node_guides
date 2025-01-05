# Delete

```bash
sudo systemctl stop subspaced subspacefarm && \
sudo systemctl disable subspaced subspacefarm 

rm -Rvf $HOME/.local/share/subspace* \
/usr/local/bin/subspace \
/usr/local/bin/farmer

sudo rm -v /etc/systemd/system/subspaced.service && \
sudo rm -v /etc/systemd/system/subspacefarm.service && \
sudo systemctl daemon-reload
```
