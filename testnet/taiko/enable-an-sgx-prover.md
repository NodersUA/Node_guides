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

## Update kernel

```bash
# install the required tools
sudo apt update
sudo apt-get install flex bison libelf-dev build-essential bc build-essential libssl-dev libncurses-dev -y

```

```bash
wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.7.1.tar.xz
tar -xvf linux-6.7.1.tar.xz
cd linux-6.7.1
cp /boot/config-$(uname -r) .config
make defconfig
make
```

```bash
sudo make modules_install install
sudo update-grub
```

```bash
reboot
```







```bash
sudo apt update
sudo apt upgrade -y
```

```bash
sudo apt dist-upgrade -y
sudo update-grub
```

```bash
 # Reboot system
reboot
```

```bash
# Check version
uname -a
```

## Raiko Docker

```bash
git clone git@github.com:taikoxyz/raiko.git
cd raiko/docker

```
