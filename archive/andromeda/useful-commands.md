***Information***
```bash
# Check the blocks
andromedad status 2>&1 | jq ."SyncInfo"."latest_block_height"
```
```bash
# Check logs
journalctl -u andromedad -f -o cat
```
```bash
# Check status
andromedad status 2>&1 | jq .SyncInfo
```
```bash
# Check balance
andromedad q bank balances $WALLET_ANDROMEDA
```
```bash
# Check pubkey of validator
andromedad tendermint show-validator
```
```bash
# Check validator
andromedad q staking validator $MONIKER_ANDROMEDA
andromedad q staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker="$MONIKER_ANDROMEDA")' | jq
```
```bash
# Check information of TX_HASH
andromedad q tx <TX_HASH>
```
```bash
# Check how many blocks were passed by the validator and from which block the asset
andromedad q slashing signing-info $(andromedad tendermint show-validator)
```

***Транзакции***
```bash
# Collect rewards from all validators delegated to them (without commission)
andromedad tx distribution withdraw-all-rewards --from <name_wallet> -y
```
```bash
# Collect rewards from a separate validator or rewards + commission from your own validator
andromedad tx distribution withdraw-rewards <valoper_address> --from <name_wallet> --commission -y
```
```bash
# Delegate yourself (this is how 1 coin is sent)
andromedad tx staking delegate $VALOPER_ANDROMEDA 1000000uandr --from wallet --fees=5000uandr -y
```
```bash
# Redelegate to other validator
andromedad tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000uandr --from wallet -y
```
```bash
# unbond 
andromedad tx staking unbond $VALOPER_ANDROMEDA 1000000uandr --from wallet -y
```
```bash
# Send tokens to other adress
andromedad tx bank send wallet <address> 1000000uandr -y
```
```bash
# Unjail
andromedad tx slashing unjail --from wallet -y
```

! If the transactions are not sent with the error account sequence mismatch, expected 18, got 17: incorrect account sequence, then add the flag -s 18 to the command (replace the number with the one that is waiting for the sequence)

***Work with wallets***
```bash
# Chech the wallets list
andromedad keys list
```
```bash
# Delete wallet
andromedad keys delete <name_wallet>
```

***Delete Node***
```bash
sudo systemctl stop andromedad && \
sudo systemctl disable andromedad && \
rm /etc/systemd/system/andromedad.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf andromedad && \
rm -rf .andromedad && \
rm -rf $(which andromedad)
```

***Governance***
```bash
# List proposals
andromedad q gov proposals
```
```bash
# Check voting result
andromedad q gov proposals --voter $VALOPER_ANDROMEDA
```
```bash
# Vote 
andromedad tx gov vote 1 yes --from $WALLET_ANDROMEDA
```
```bash
# Make Deposit for proposal
humansd tx gov deposit 1 1000000uheart --from $WALLET_ANDROMEDA
```

***Peers and RPC***
```bash
FOLDER=.andromedad

# Check your peer
PORTR=$(grep -A 3 "\[p2p\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+") && \
echo $(andromedad tendermint show-node-id)@$(curl ifconfig.me)$PORTR

# Check port of RPC
echo -e "\033[0;32m$(grep -A 3 "\[rpc\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+")\033[0m"

# Check peers quantity
PORT=
curl -s http://localhost:$PORT_ANDROMEDA/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr | split(":")[2])"' | wc -l
```

