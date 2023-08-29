# Useful Commands

### Information

```bash
# Check the blocks
defundd status 2>&1 | jq ."SyncInfo"."latest_block_height"

# Restart
systemctl restart defundd && journalctl -u defundd -f -o cat

# Check logs
journalctl -u defundd -f -o cat

# Check status
defundd status 2>&1 | jq .SyncInfo

# Check balance
defundd q bank balances $DEFUND_ADDRESS

# Check pubkey of validator
defundd tendermint show-validator

# Check validator
defundd q staking validator $DEFUND_VALOPER
defundd q staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker="$DEFUND_VALOPER")' | jq

# Check information of TX_HASH
defundd q tx <TX_HASH>

# Check how many blocks were passed by the validator and from which block the asset
defundd q slashing signing-info $(defundd tendermint show-validator)
```

```bash
Transactions

# Collect rewards from all validators delegated to them (without commission)
defundd tx distribution withdraw-all-rewards --from wallet --fees 500ufetf -y

# Collect rewards from a separate validator or rewards + commission from your own validator
defundd tx distribution withdraw-rewards $DEFUND_VALOPER --from wallet --fees 500ufetf --commission -y

# Delegate yourself (this is how 1 coin is sent)
defundd tx staking delegate $DEFUND_VALOPER 1000000ufetf --from wallet --fees 500ufetf -y

# Redelegate to other validator
defundd tx staking redelegate $DEFUND_VALOPER <dst-validator-addr> 1000000ufetf --from wallet --fees 500ufetf -y

# unbond 
defundd tx staking unbond $DEFUND_VALOPER 1000000ufetf --from wallet --fees 500ufetf -y

# Send tokens to other adress
defundd tx bank send wallet <address> 1000000ufetf --fees 500ufetf -y

# Escape from jail
defundd tx slashing unjail --from wallet --fees 500ufetf
```

! If the transactions are not sent with the error account sequence mismatch, expected 18, got 17: incorrect account sequence, then add the flag -s 18 to the command (replace the number with the one that is waiting for the sequence)

**Work with wallets**

```bash
# Chech the wallets list
defundd keys list

# Delete wallet
defundd keys delete wallet
```

Governance

```bash
# List proposals
defundd q gov proposals

# Check voting result
defundd q gov proposals --voter $DEFUND_VALOPER

# Vote 
defundd tx gov vote 2 yes --from wallet
```

**Peers and RPC**

```bash
FOLDER=.defundd

# Check your peer
PORTR=$(grep -A 3 "\[p2p\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+") && \
echo $(defundd tendermint show-node-id)@$(curl ifconfig.me)$PORTR

# Check port of RPC
echo -e "\033[0;32m$(grep -A 3 "\[rpc\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+")\033[0m"

# Check peers quantity
PORT=
curl -s http://localhost:$DEFUND_PORT/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr | split(":")[2])"' | wc -l
```
