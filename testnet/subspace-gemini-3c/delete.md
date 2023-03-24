# Delete

```bash
sudo systemctl stop subspaced && \
sudo systemctl disable subspaced

rm -Rvf $HOME/.local/share/subspace* \
$HOME/.config/subspace* \
/usr/local/bin/subspace

sudo rm -v /etc/systemd/system/subspaced.service && \
sudo systemctl daemon-reloads
```
