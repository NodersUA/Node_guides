# Useful Commands

## Lava

```bash
# Check the blocks
lavad status 2>&1 | jq ."SyncInfo"."latest_block_height"

# Check logs
journalctl -u lavad -f -o cat

# Restart
systemctl restart lavad && journalctl -u lavad -f -o cat

# Check status
lavad status 2>&1 | jq .SyncInfo

# Check balance
lavad q bank balances $LAVA_ADDRESS
```

## Axelar Testnet

```bash
# Check the blocks
t_axelar status 2>&1 | jq ."SyncInfo"."latest_block_height"

# Check logs
journalctl -u t_axelar -f -o cat

# Restart
systemctl restart t_axelar && journalctl -u t_axelar -f -o cat

# Check status
t_axelar status 2>&1 | jq .SyncInfo

# Check balance
t_axelar q bank balances $AXELAR_TESTNET_ADDRESS
```

## Axelar Mainnet

```bash
# Check the blocks
m_axelar status 2>&1 | jq ."SyncInfo"."latest_block_height"

# Check logs
journalctl -u m_axelar -f -o cat

# Restart
systemctl restart m_axelar && journalctl -u m_axelar -f -o cat

# Check status
m_axelar status 2>&1 | jq .SyncInfo

# Check balance
m_axelar q bank balances $AXELAR_MAINNET_ADDRESS
```
