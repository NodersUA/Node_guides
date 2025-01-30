# Multiple

```bash
wget https://cdn.app.multiple.cc/client/linux/x64/multipleforlinux.tar
tar -xvf multipleforlinux.tar
rm multipleforlinux.tar
cd multipleforlinux/
chmod +x ./multiple-cli
chmod +x ./multiple-node
PATH=$PATH:$(pwd)
source /etc/profile
cd && chmod -R 777 multipleforlinux
nohup ./multipleforlinux/multiple-node > output.log 2>b&1 &
```

```
IDENTITY=
PIN=
```

```bash
/root/multipleforlinux/multiple-cli bind --bandwidth-download 100 --identifier $IDENTITY --pin $PIN --storage 200 --bandwidth-upload 100
```
