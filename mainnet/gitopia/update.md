# Update

```bash
# Update to v2.1.0

cd $HOME && rm -rf gitopia
curl https://get.gitopia.com | bash
git clone -b v2.1.0 gitopia://gitopia/gitopia
cd gitopia && make install

systemctl stop gitopiad
sudo cp ~/go/bin/gitopiad ~/.gitopia/cosmovisor/genesis/bin
sudo cp ~/go/bin/gitopiad /usr/local/bin/gitopiad
systemctl restart gitopiad
gitopiad version

```
