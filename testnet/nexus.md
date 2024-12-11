# Nexus

```bash
sudo apt update
sudo apt install build-essential pkg-config libssl-dev git-all -y
sudo apt install -y protobuf-compiler
sudo apt install cargo -y
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
echo 'export PATH="$HOME/.cargo/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
rustup update
```

```bash
curl https://cli.nexus.xyz/ | sh
```

