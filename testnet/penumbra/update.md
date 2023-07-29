# Update

```bash
cd ~/penumbra && git fetch && git checkout v0.57.0
cargo build --release --bin pcli
cp ~/penumbra/target/release/pcli /usr/local/bin
cargo run --quiet --release --bin pcli view reset
```
