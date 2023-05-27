***Information***
```bash
# Check the blocks
dymd status 2>&1 | jq ."SyncInfo"."latest_block_height"
```
```bash
# Check logs
journalctl -u dymd -f -o cat
```
```bash
# Check status
dymd status 2>&1 | jq .SyncInfo
```
```bash
# Check balance
dymd q bank balances $WALLET_DYMENSION
```
```bash
# Check pubkey of validator
dymd tendermint show-validator
```
```bash
# Check validator
dymd q staking validator $VALOPER_DYMENSION
dymd q staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker="$MONIKER_DYMENSION")' | jq
```
```bash
# Check information of TX_HASH
dymd q tx <TX_HASH>
```
```bash
# Check how many blocks were passed by the validator and from which block the asset
dymd q slashing signing-info $(dymd tendermint show-validator)
```

***Transactions***
```bash
# Collect rewards from all validators delegated to them (without commission)
dymd tx distribution withdraw-all-rewards --from <name_wallet> --fees 5000udym -y
```
```bash
# Collect rewards from a separate validator or rewards + commission from your own validator
dymd tx distribution withdraw-rewards <valoper_address> --from <name_wallet> --fees 5000udym --commission -y
```
```bash
# Delegate yourself (this is how 1 coin is sent)
dymd tx staking delegate <valoper_address> 1000000udym --from <name_wallet> --fees 5000udym -y
```
```bash
# Redelegate to other validator
dymd tx staking redelegate <src-validator-addr> <dst-validator-addr> 1000000udym --from <name_wallet> --fees 5000udym -y
```
```bash
# Unbond 
dymd tx staking unbond <addr_valoper> 1000000udym --from <name_wallet> --fees 5000udym -y
```
```bash
# Send tokens to other adress
dymd tx bank send <name_wallet> <address> 1000000udym --fees 5000udym -y
```
```bash
# Unjail
dymd tx slashing unjail --from <name_wallet> --fees 5000udym -y
```

! If the transactions are not sent with the error account sequence mismatch, expected 18, got 17: incorrect account sequence, then add the flag -s 18 to the command (replace the number with the one that is waiting for the sequence)

***Work with wallets***
```bash
# Chech the wallets list
dymd keys list
```
```bash
# Delete wallet
dymd keys delete <name_wallet>
```

***Delete Node***
```bash
sudo systemctl stop dymd && \
sudo systemctl disable dymd && \
rm /etc/systemd/system/dymd.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf dymension && \
rm -rf .dymension && \
rm -rf $(which dymd)
```

***Governance***
```bash
# List proposals
dymd q gov proposals
```
```bash
# Check voting result
dymd q gov proposals --voter $VALOPER_DYMENSION
```
```bash
# Vote
dymd tx gov vote 1 yes --from $WALLET_DYMENSION
```
```bash
# Make Deposit for proposal
dymd tx gov deposit 1 1000000uheart --from $WALLET_DYMENSION
```

***Peers and RPC***
```bash
FOLDER=.dymension

# Check your peer
PORTR=$(grep -A 3 "\[p2p\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+") && \
echo $(dymd tendermint show-node-id)@$(curl ifconfig.me)$PORTR

# Check port of RPC
echo -e "\033[0;32m$(grep -A 3 "\[rpc\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+")\033[0m"

# Check peers quantity
PORT=
curl -s http://localhost:$PORT_DYMENSION/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr | split(":")[2])"' | wc -l
```
