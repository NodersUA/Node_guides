# Enable an SGX prover

Run `cpuid` and `grep` for SGX:

```bash
cpuid | grep -i sgx
```

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

#### Modern Linux kernel

Starting with Linux kernel version [`5.11`](https://www.intel.com/content/www/us/en/developer/tools/software-guard-extensions/linux-overview.html), the kernel provides out-of-the-box support for SGX. However, it doesn't support [EDMM](https://gramine.readthedocs.io/en/stable/manifest-syntax.html#edmm) (Enclave Dynamic Memory Management), which Raiko requires. EDMM support first appeared in Linux `6.0`, so ensure that you have Linux kernel `6.0` or above.

To check version of your kernel run:

```
uname -a
```

## Update ubuntu and kernel

```bash
# Update the repositories
apt update && apt upgrade -y
```

```bash
reboot
```

```bash
ufw allow 1022
```

```bash
sudo do-release-upgrade
```

```bash
sudo apt install linux-image-unsigned-6.5.0-15-generic
sudo update-initramfs -u -k 6.5.0-15-generic
```

```bash
reboot
```

## Raiko Docker

```bash
# Update or install rust
if command -v rustup &> /dev/null; then
    rustup update
else
    curl https://sh.rustup.rs -sSf | sh
    source $HOME/.cargo/env
fi
rustc --version # Verify Rust installation by displaying the version
```

```bash
git clone https://github.com/johntaiko/zeth.git
cd zeth
sed -i 's/sgx.edmm_enable = true/sgx.edmm_enable = false/' raiko-guest/config/raiko-guest.manifest.template
DOCKER_BUILDKIT=0 docker build -t raiko:v1 . 
```

```bash
cd docker
sed -i 's/image: gcr\.io\/evmchain\/raiko:latest/image: raiko:v1/' docker-compose.yml
sed -i 's/8080:8080/8585:8585/' docker-compose.yml
docker compose run --rm raiko --init
docker compose up raiko -d
```
