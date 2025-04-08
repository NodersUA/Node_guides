# Gensyn

```bash
sudo apt-get update
sudo apt install screen curl iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev  -y
```

```bash
# Install docker
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/system/docker)
```

```bash
sudo apt-get install python3 python3-pip python3-venv python3-dev -y
sudo apt-get update
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt-get install -y nodejs
node -v
sudo npm install -g yarn
yarn -v
curl -o- -L https://yarnpkg.com/install.sh | bash
export PATH="$HOME/.yarn/bin:$HOME/.config/yarn/global/node_modules/.bin:$PATH"
source ~/.bashrc
```

```bash
ufw allow 3000
```

```bash
git clone https://github.com/gensyn-ai/rl-swarm/
```

```bash
cd rl-swarm
python3 -m venv .venv
source .venv/bin/activate
#./run_rl_swarm.sh
```

```bash
sudo tee /etc/systemd/system/gensyn.service > /dev/null <<EOF
[Unit]
Description=RL Swarm Service
After=network.target

[Service]
Type=simple
WorkingDirectory=/root/rl-swarm
ExecStart=/bin/bash -c 'source /root/rl-swarm/.venv/bin/activate && /root/rl-swarm/run_rl_swarm.sh'
Restart=always
User=root
Group=root
Environment="PATH=/root/rl-swarm/.venv/bin:/usr/local/bin:/usr/bin:/bin"

[Install]
WantedBy=multi-user.target
EOF
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable gensyn.service
sudo systemctl restart gensyn.service
```

## Troubleshooting:

```bash
cd rl-swarm
nano hivemind_exp/configs/mac/grpo-qwen-2.5-0.5b-deepseek-r1.yaml
```

* Lower `max_steps` to `5`

```bash
cd modal-login
yarn install

yarn upgrade && yarn add next@latest && yarn add viem@latest

cd ..
```
