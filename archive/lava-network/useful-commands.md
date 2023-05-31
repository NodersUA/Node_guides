# Useful commands

### Information <a href="#m7xp" id="m7xp"></a>

```bash
# Check bloks
lavad status 2>&1 | jq ."SyncInfo"."latest_block_height"

# Check the logs
journalctl -u lavad -f -o cat

# Check status
curl localhost:${PORT_LAVA}657/status

# Check balance
lavad q bank balances $WALLET_LAVA

# Check pubkey of validator
lavad tendermint show-validator

# Check validator
lavad query staking validator $VALOPER_LAVA
lavad query staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker=="<name_moniker>")' | jq

# Check info of TX_HASH
lavad query tx <TX_HASH>

# Check how many blocks were passed by the validator and from which block the asset
lavad q slashing signing-info $(lavad tendermint show-validator)
```

```bash
Transactions

# Collect rewards from all validators delegated to them (without commission)
lavad tx distribution withdraw-all-rewards --from <name_wallet> --fees 5000ulava -y

# Collect rewards from a separate validator or rewards + commission from your own validator
lavad tx distribution withdraw-rewards <valoper_address> --from <name_wallet> --fees 5000ulava --commission -y

# Delegate yourself (this is how 1 coin is sent)
lavad tx staking delegate <valoper_address> 1000000ulava --from <name_wallet> --fees 5000ulava -y

# Redelegate to other validator
lavad tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000ulava --from <name_wallet> --fees 5000ulava -y

# unbond 
lavad tx staking unbond <addr_valoper> 1000000ulava --from <name_wallet> --fees 5000ulava -y

# Send tokens to other adress
lavad tx bank send <name_wallet> <address> 1000000ulava --fees 5000ulava -y

# Escape from jail
lavad tx slashing unjail --from <name_wallet> --fees 5000ulava -y
```

! If the transactions are not sent with the error account sequence mismatch, expected 18, got 17: incorrect account sequence, then add the flag -s 18 to the command (replace the number with the one that is waiting for the sequence)

**Work with wallet**

```bash
# Check list of the wallets
lavad keys list

# Delete wallet
lavad keys delete <name_wallet>
```

**Delete the node**

```bash
systemctl stop lavad && \
systemctl disable lavad && \
rm /etc/systemd/system/lavad.service && \
systemctl daemon-reload && \
cd $HOME && \
rm -rf .lava GHFkqmTzpdNLDd6T lava && \
rm -rf $(which lavad)
```

Governance

```bash
# List of proposals
lavad q gov proposals

# Check vote result
lavad q gov proposals --voter <ADDRESS>

# Vote 
lavad tx gov vote 1 yes --from <name_wallet> --fees 555ulava

# Send deposit for proposal
lavad tx gov deposit 1 1000000ulava --from <name_wallet> --fees 555ulava
```

**Peers and RPC**

```bash
FOLDER=.lava

# Check your peer
PORTR=$(grep -A 3 "\[p2p\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+") && \
echo $(lavad tendermint show-node-id)@$(curl ifconfig.me)$PORTR

# Check your port RPC
echo -e "\033[0;32m$(grep -A 3 "\[rpc\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+")\033[0m"

# Check quantity of ports
PORT=
curl -s http://localhost:$PORT/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr | split(":")[2])"' | wc -lsh
```
