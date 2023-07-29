# Update

```bash
cd ~/penumbra && git fetch && git checkout v0.57.0
cargo build --release --bin pcli
cargo run --quiet --release --bin pcli view reset
```
