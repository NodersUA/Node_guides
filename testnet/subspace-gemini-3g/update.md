# Update

```bash
sudo systemctl stop subspaced subspacefarm
mkdir $HOME/subspace
cd $HOME/subspace
wget https://github.com/subspace/subspace/releases/download/gemini-3g-2023-nov-14/subspace-farmer-ubuntu-x86_64-skylake-gemini-3g-2023-nov-14 -O farmer
wget https://github.com/subspace/subspace/releases/download/gemini-3g-2023-nov-14/subspace-node-ubuntu-x86_64-skylake-gemini-3g-2023-nov-14 -O subspace
sudo chmod +x *
rm /usr/local/bin/farmer
rm /usr/local/bin/subspace
sudo mv * /usr/local/bin/
cd $HOME && \
rm -Rvf $HOME/subspace
sudo systemctl restart subspacefarm subspaced
```
