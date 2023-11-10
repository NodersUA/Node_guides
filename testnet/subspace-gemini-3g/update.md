# Update

```bash
sudo systemctl stop subspaced subspacefarm
mkdir $HOME/subspace
cd $HOME/subspace
wget https://github.com/subspace/subspace/releases/download/gemini-3g-2023-nov-09/subspace-farmer-ubuntu-x86_64-skylake-gemini-3g-2023-nov-09 -O farmer
wget https://github.com/subspace/subspace/releases/download/gemini-3g-2023-nov-09/subspace-node-ubuntu-x86_64-skylake-gemini-3g-2023-nov-09 -O subspace
sudo chmod +x *
rm /usr/local/bin/farmer
rm /usr/local/bin/subspace
sudo mv * /usr/local/bin/
cd $HOME && \
rm -Rvf $HOME/subspace
```

```bash
sudo tee <<EOF >/dev/null /etc/systemd/system/subspacefarm.service
[Unit]
Description=Subspace Farmer
After=network.target
[Service]
Type=simple
User=$USER
ExecStart=$(which farmer) farm --reward-address=$SUBSPACE_ADDRESS path=$HOME/.local/share/subspace-farmer,size=${PLOT_SIZE}G --farm-during-initial-plotting
Restart=always
RestartSec=10
LimitNOFILE=1024000
[Install]
WantedBy=multi-user.target
EOF
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable subspaced subspacefarm
sudo systemctl restart subspacefarm subspaced
```
