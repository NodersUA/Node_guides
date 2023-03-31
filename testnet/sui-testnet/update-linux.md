# Update (Linux)

```bash
# 287d37841adf35848e846e355f92dcaaa124fbd8
cd $HOME/sui && \
git fetch upstream && \
git checkout 287d37841adf35848e846e355f92dcaaa124fbd8 && \
cargo build --release -p sui-node -p sui && \
mv $HOME/sui/target/release/sui-node /usr/local/bin/ && \
mv $HOME/sui/target/release/sui /usr/local/bin/ && \
sudo systemctl restart suid && \
sudo journalctl -fn 100 -u suid
```
