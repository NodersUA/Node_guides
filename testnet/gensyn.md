# Gensyn

```bash
sudo apt-get update
sudo apt install screen curl cmdtest iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev  -y
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
screen -S gensyn
```

```bash
cd /root/rl-swarm && python3 -m venv .venv && source .venv/bin/activate
#./run_rl_swarm.sh
```

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # Завантажує nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # Додає автодоповнення
```

```bash
nvm install 18 && nvm use 18
```

```bash
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt update && sudo apt install yarn
```

```bash
cd modal-login && yarn install

yarn upgrade && yarn add next@latest && yarn add viem@latest

cd .. && ./run_rl_swarm.sh
```

## Troubleshooting:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # Завантажує nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # Додає автодоповнення
```

```bash
source ~/.bashrc
cd /root/rl-swarm
source .venv/bin/activate
nvm install 18
nvm use 18
```

```bash
cd /root/rl-swarm
nano hivemind_exp/configs/mac/grpo-qwen-2.5-0.5b-deepseek-r1.yaml
```

* Lower `max_steps` to `5`

```bash
cd modal-login
yarn install

yarn upgrade && yarn add next@latest && yarn add viem@latest

cd ..
```
