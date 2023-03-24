# Installation

### Node

```bash
# Update the repositories
apt update && apt upgrade -y
```

```bash
# Install developer packages (One command)
sudo apt-get install wget jq ocl-icd-opencl-dev \
libopencl-clang-dev libgomp1 ocl-icd-libopencl1 -y
```

<pre class="language-bash"><code class="lang-bash"># # Download binary files (One command)
mkdir $HOME/subspace >/dev/null 2>&#x26;1 &#x26;&#x26; \
cd $HOME/subspace &#x26;&#x26; \
VER=$(wget -qO- https://api.github.com/repos/subspace/subspace-cli/releases | jq '.[] | select(.prerelease==false) | select(.draft==false) | .html_url' | grep -Eo "v[0-9]*.[0-9]*.[0-9]*-alpha" | head -n 1) &#x26;&#x26; \
<strong>wget https://github.com/subspace/subspace-cli/releases/download/${VER}/subspace-cli-ubuntu-x86_64-${VER} -qO subspace; \
</strong>sudo chmod +x * &#x26;&#x26; \
sudo mv * /usr/local/bin/ &#x26;&#x26; \
echo -e "\n\nrelease >> ${VER}.\n\n" &#x26;&#x26; \
cd $HOME &#x26;&#x26; \
rm -Rvf $HOME/subspace >/dev/null 2>&#x26;1
</code></pre>

Create your [polkadot.js](https://teletype.in/@muhaylo.semenyuk/7SVtg0CRgDF) wallet or import wallet from gemini 2

```bash
subspace init
```

1. Enter your [polkadot.js](https://teletype.in/@muhaylo.semenyuk/7SVtg0CRgDF) address
2. Enter your NodeName
3. Press Enter
4. Specify a plot size (min 50 GB, example: 50 GB, 1100 GB)
5. Press Enter

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

```bash
# Fix journal (One command)
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
```

```bash
# Restart journal
sudo systemctl restart systemd-journald
```

```bash
# Create service file (One command)
sudo tee <<EOF >/dev/null /etc/systemd/system/subspaced.service
[Unit]
Description=Subspace Node
After=network.target
[Service]
Type=simple
User=$USER
ExecStart=$(which subspace) farm -v
Restart=always
RestartSec=3
LimitNOFILE=1024000
[Install]
WantedBy=multi-user.target
EOF
```

```bash
# Start the node
sudo systemctl daemon-reload && \
sudo systemctl enable subspaced && \
sudo systemctl restart subspaced
```

```bash
# Check the logs
sudo journalctl -fu subspaced --no-hostname -o cat
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Check your node in [telemetry](https://telemetry.subspace.network/#list/0xab946a15b37f59c5f4f27c5de93acde9fe67a28e0b724a43a30e4fe0e87246b7)

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>
