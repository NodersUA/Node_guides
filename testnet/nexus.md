# Nexus

```bash
screen -S nexus
```

```bash
sudo apt update && sudo apt install build-essential pkg-config libssl-dev git-all -y
```

```bash
sudo apt install -y protobuf-compiler
```

```bash
sudo apt install cargo -y
```

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

```bash
source $HOME/.cargo/env
echo 'export PATH="$HOME/.cargo/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
rustup update
```

```bash
curl https://cli.nexus.xyz/ | sh
```

