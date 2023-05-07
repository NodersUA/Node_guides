# Useful Commands

**Use NVM to install Node**

```bash
nvm install node
nvm install 16.18.0
```

```bash
nvm use 16.18.0
```

```bash
# Check ballance
npx @bundlr-network/testnet-cli@latest balance $BUNDLR_ADDRESS
```

```bash
# Check validator is active
nvm use 16.18.0 && npx @bundlr-network/testnet-cli@latest check $GW_CONTRACT $BUNDLR_ADDRESS
```
