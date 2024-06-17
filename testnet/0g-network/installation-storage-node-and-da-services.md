# Installation (Storage Node And DA Services)

```bash
sudo apt-get update
sudo apt-get install clang cmake build-essential
```

```bash
# Install Go
source <(curl -s https://raw.githubusercontent.com/NodersUA/Scripts/main/system/go)
```

```bash
git clone -b v0.3.1 https://github.com/0glabs/0g-storage-node.git
cd 0g-storage-node
git submodule update --init

# Build in release mode
cargo build --release
```

```bash
```
