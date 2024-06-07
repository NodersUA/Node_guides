# Useful Commands

## Information

```bash
# Check the blocks
0gchaind status 2>&1 | jq ."sync_info"."latest_block_height"

# Check logs
journalctl -u 0gd -f -o cat

# Restart
systemctl restart 0gd && journalctl -u 0gd -f -o cat

# Check your EVM Address
echo "0x$(0gchaind debug addr $(0gchaind keys show wallet -a) | grep hex | awk '{print $3}')"

# Check status
0gchaind status 2>&1 | jq .sync_info

# Check balance
0gchaind q bank balances $0G_ADDRESS

# Check pubkey of validator
0gchaind tendermint show-validator

# Check validator
0gchaind q staking validator $0G_VALOPER
0gchaind q staking validators --limit 1000000 -o json | jq '.validators[] | select(.description.moniker="$0G_VALOPER")' | jq

# Check information of TX_HASH
0gchaind q tx <TX_HASH>

# Check how many blocks were passed by the validator and from which block the asset
0gchaind q slashing signing-info $(0gchaind tendermint show-validator)
```

```bash
Transactions

# Collect rewards from all validators delegated to them (without commission)
0gchaind tx distribution withdraw-all-rewards --from wallet --gas="auto" --gas-adjustment=1.4 -y

# Collect rewards from a separate validator or rewards + commission from your own validator
0gchaind tx distribution withdraw-rewards $OG_VALOPER --from wallet --gas="auto" --gas-adjustment=1.4 --commission -y

# Delegate yourself (this is how 1 coin is sent)
0gchaind tx staking delegate $0G_VALOPER 1000000ua0gi --from wallet --gas="auto" --gas-adjustment=1.4 -y

# Redelegate to other validator
0gchaind tx staking redelegate $0G_VALOPER <dst-validator-addr> 1000000ua0gi --from wallet --gas="auto" --gas-adjustment=1.4 -y

# unbond 
0gchaind tx staking unbond $0G_VALOPER 1000000ua0gi --from wallet --gas="auto" --gas-adjustment=1.4 -y

# Send tokens to other adress
0gchaind tx bank send wallet <address> 1000000ua0gi --gas="auto" --gas-adjustment=1.4 -y

# Escape from jail
0gchaind tx slashing unjail --from wallet --gas="auto" --gas-adjustment=1.4 -y
```

! If the transactions are not sent with the error account sequence mismatch, expected 18, got 17: incorrect account sequence, then add the flag -s 18 to the command (replace the number with the one that is waiting for the sequence)

## **Work with wallets**

```bash
# Chech the wallets list
0gchaind keys list

# Delete wallet
0gchaind keys delete <name_wallet>
```

## **Delete Node**

```bash
sudo systemctl stop 0gd && \
sudo systemctl disable 0gd && \
rm /etc/systemd/system/0gd.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf 0g-chain && \
rm -rf .0gchain && \
rm -rf $(which 0gchaind)
```

## Governance

```bash
# List proposals
0gchaind q gov proposals

# Check voting result
0gchaind q gov proposals --voter $0G_ADDRESS

# Vote 
0gchaind tx gov vote 20 yes --from wallet --gas="auto" --gas-adjustment=1.2 --gas-prices="0.00001ua0gi" -y
```

## **Peers and RPC**

```bash
FOLDER=.0gchain

# Check your peer
PORTR=$(grep -A 3 "\[p2p\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+") && \
echo $(0gchaind tendermint show-node-id)@$(curl ifconfig.me)$PORTR

# Check port of RPC
echo -e "\033[0;32m$(grep -A 3 "\[rpc\]" ~/$FOLDER/config/config.toml | egrep -o ":[0-9]+")\033[0m"

# Check peers quantity
PORT=
curl -s http://localhost:BABYLON_PORT/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr | split(":")[2])"' | wc -l
```
