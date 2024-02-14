# Update

```bash
sudo systemctl stop subspaced subspacefarm
rm -rf /root/.local/share/subspace-farmer/
rm -rf /root/.local/share/subspace-node/
mkdir $HOME/subspace
cd $HOME/subspace
wget https://github.com/subspace/subspace/releases/download/gemini-3h-2024-feb-05/subspace-farmer-ubuntu-x86_64-skylake-gemini-3h-2024-feb-05 -O farmer
wget https://github.com/subspace/subspace/releases/download/gemini-3h-2024-feb-05/subspace-node-ubuntu-x86_64-skylake-gemini-3h-2024-feb-05 -O subspace
sudo chmod +x *
rm /usr/local/bin/farmer
rm /usr/local/bin/subspace
sudo mv * /usr/local/bin/
cd $HOME && \
rm -Rvf $HOME/subspace

sudo tee <<EOF >/dev/null /etc/systemd/system/subspaced.service
[Unit]
Description=Subspace Node
After=network.target
[Service]
Type=simple
User=$USER
ExecStart=$(which subspace) run \\
--base-path $HOME/.local/share/subspace-node \\
--chain="gemini-3h" \\
--blocks-pruning="256" \\
--state-pruning="archive-canonical" \\
--farmer \\
--name="$MONIKER"
Restart=on-failure
RestartSec=10
LimitNOFILE=1024000
[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl restart subspacefarm subspaced
```
