# Update

```bash
cd ~/taiko && docker compose down -v
git pull origin main && docker compose pull
```

```bash
cp .env.sample .env
```

```bash
# Set the variables
RPC_HOLESKY=<your_holesky_rpc>
WS_HOLESKY=<your_holesky_ws>
L1_PROPOSER_PRIVATE_KEY=<your_private_key>
EVM_ADDRESS=<your_evm_address>

echo 'export RPC_HOLESKY='$RPC_HOLESKY >> $HOME/.bash_profile
echo 'export WS_HOLESKY='$WS_HOLESKY >> $HOME/.bash_profile
echo 'export EVM_ADDRESS='$EVM_ADDRESS >> $HOME/.bash_profile
source $HOME/.bash_profile
```

```bash
sed -i "s|^L1_ENDPOINT_HTTP=.*|L1_ENDPOINT_HTTP=$RPC_HOLESKY|" .env
sed -i "s|^L1_ENDPOINT_WS=.*|L1_ENDPOINT_WS=$WS_HOLESKY|" .env
sed -i 's/^PORT_GRAFANA=.*/PORT_GRAFANA=4301/' .env
sed -i 's/^PORT_PROMETHEUS=.*/PORT_PROMETHEUS=9043/' .env
sed -i 's/^ENABLE_PROPOSER=.*/ENABLE_PROPOSER=true/' .env
sed -i "s/^L1_PROPOSER_PRIVATE_KEY=.*/L1_PROPOSER_PRIVATE_KEY=$L1_PROPOSER_PRIVATE_KEY/" .env
sed -i "s/^L2_SUGGESTED_FEE_RECIPIENT=.*/L2_SUGGESTED_FEE_RECIPIENT=$EVM_ADDRESS/" .env
```

```bash
docker compose --profile l2_execution_engine up -d
docker compose --profile prover up -d
```

```bash
# Check logs
cd ~/taiko && docker-compose logs -f --tail 100
```

```bash
# Restart
cd ~/taiko && docker compose down && docker compose up -d
```
