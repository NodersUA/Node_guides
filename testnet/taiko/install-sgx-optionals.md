# Install SGX (Optionals)

## Install SGX Linux Driver for Intel(R) SGX DCAP&#x20;

### Update kernel

```bash
# install the required tools
sudo apt update
sudo apt install linux-generic-hwe-20.04 -y
```

```bash
sudo apt update
sudo apt upgrade -y
sudo apt dist-upgrade -y
sudo update-grub -y
```

```bash
 # Reboot system
reboot
```

```bash
# Check version
uname -a
```

### Build and Install the Intel(R) SGX Driver

```bash
# Install driver
wget https://download.01.org/intel-sgx/latest/linux-latest/distro/ubuntu22.04-server/sgx_linux_x64_driver_2.11.54c9c4c.bin
chmod +x sgx_linux_x64_driver_2.11.54c9c4c.bin
./sgx_linux_x64_driver_2.11.54c9c4c.bin
```

Specify the directory to install the sdk, for example `/opt/intel/`

```bash
# Install SGX SDK
wget https://download.01.org/intel-sgx/latest/linux-latest/distro/ubuntu20.04-server/sgx_linux_x64_sdk_2.23.100.2.bin
chmod +x sgx_linux_x64_sdk_2.23.100.2.bin
./sgx_linux_x64_sdk_2.23.100.2.bin
```

```bash
source /opt/intel/sgxsdk/environment
```

```bash
sudo apt-get install libboost-dev libboost-all-dev file libcurl4-openssl-dev -y
```

```bash
cd SGXDataCenterAttestationPrimitives/QuoteGeneration
./download_prebuilt.sh
```

```bash
git clone --recursive https://github.com/intel/SGXDataCenterAttestationPrimitives
cd SGXDataCenterAttestationPrimitives/QuoteGeneration
./download_prebuilt.sh
cd ..
make
make clean
```

### Build and run with Docker

```bash
sudo apt-get install dkms -y

# sgx_ver=$(grep -oP 'PACKAGE_VERSION="\K[^"]+' /opt/intel/linux-sgx/external/dcap_source/driver/linux/dkms.conf)
sgx_ver=$(grep -oP 'PACKAGE_VERSION="\K[^"]+' /etc/modprobe.d/dkms.conf)
sudo mkdir -p /usr/src/sgx-$sgx_ver
sudo cp -rp /opt/intel/linux-sgx/external/dcap_source/driver/linux/* /usr/src/sgx-$sgx_ver/
```

```bash
sudo dkms add -m sgx -v $sgx_ver
sudo dkms build -m sgx -v $sgx_ver
sudo dkms install -m sgx -v $sgx_ver
sudo /sbin/modprobe intel_sgx
```
