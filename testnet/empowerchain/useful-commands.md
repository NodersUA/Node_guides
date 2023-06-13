# Useful Commands

### Information

```bash
# Check the blocks
empowerd status 2>&1 | jq ."SyncInfo"."latest_block_height"

# Check logs
journalctl -u empowerd -f -o cat

# Restart
systemctl restart empowerd && journalctl -u empowerd -f -o cat

# Check status
empowerd status 2>&1 | jq .SyncInfo

# Check balance
empowerd q bank balances $EMPOWER_ADDRESS

# Check EVM address
empowerd address-converter $(empowerd keys show wallet -a)

# Check pubkey of validator
empowerd tendermint show-validator

# Check validator
empowerd q staking validator $EMPOWER_VALOPER
empowerd q staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker="$EMPOWER_VALOPER")' | jq

# Check information of TX_HASH
empowerd q tx <TX_HASH>

# Check how many blocks were passed by the validator and from which block the asset
empowerd q slashing signing-info $(empowerd tendermint show-validator)
```

```bash
Transactions

# Collect rewards from all validators delegated to them (without commission)
empowerd tx distribution withdraw-all-rewards --from wallet --gas auto --gas-adjustment=1.2 --gas-prices=0.025umpwr -y

# Collect rewards from a separate validator or rewards + commission from your own validator
empowerd tx distribution withdraw-rewards $EMPOWER_VALOPER --from wallet --gas auto --gas-adjustment=1.2 --gas-prices=0.025umpwr --commission -y

# Delegate yourself (this is how 1 coin is sent)
empowerd tx staking delegate $EMPOWER_VALOPER 1000000umpwr --from wallet --gas auto --gas-adjustment=1.2 --gas-prices=0.025umpwr -y

# Redelegate to other validator
empowerd tx staking redelegate $EMPOWER_VALOPER <dst-validator-addr> 1000000umpwr --from wallet --gas auto --gas-adjustment=1.2 --gas-prices=0.025umpwr -y

# unbond 
empowerd tx staking unbond $EMPOWER_VALOPER 1000000umpwr --from wallet --gas auto --gas-adjustment=1.2 --gas-prices=0.025umpwr -y

# Send tokens to other adress
empowerd tx bank send wallet <address> 1000000umpwr --gas auto --gas-adjustment=1.2 --gas-prices=0.025umpwr -y

# Escape from jail
empowerd tx slashing unjail --from wallet --gas auto --gas-adjustment=1.2 --gas-prices=0.025umpwr
```

! If the transactions are not sent with the error account sequence mismatch, expected 18, got 17: incorrect account sequence, then add the flag -s 18 to the command (replace the number with the one that is waiting for the sequence)

**Work with wallets**

```bash
# Chech the wallets list
empowerd keys list

# Delete wallet
empowerd keys delete <name_wallet>
```

**Delete Node**

```bash
sudo systemctl stop empowerd && \
sudo systemctl disable empowerd && \
rm /etc/systemd/system/empowerd.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf empowerchain && \
rm -rf .empowerchain && \
rm -rf $(which empowerd)
```

Governance

```bash
# List proposals
empowerd q gov proposals

# Check voting result
empowerd q gov proposals --voter $EMPOWER_VALOPER

# Vote 
empowerd tx gov vote 1 yes --from $EMPOWER_ADRESS
```

**Peers and RPC**

```bash
FOLDER=.empowerchain

# Check your peer
PORTR=$(grep -A 3 "\[p2p\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+") && \
echo $(empowerd tendermint show-node-id)@$(curl ifconfig.me)$PORTR

# Check port of RPC
echo -e "\033[0;32m$(grep -A 3 "\[rpc\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+")\033[0m"

# Check peers quantity
PORT=
curl -s http://localhost:EMPOWER_PORT/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr | split(":")[2])"' | wc -l
```
