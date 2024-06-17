# Delete

```bash
docker stop taiko-taiko_client_prover_relayer-1
docker stop taiko-taiko_client_driver-1
docker stop taiko-l2_execution_engine-1
docker stop taiko-zkevm_chain_prover_rpcd-1
docker stop taiko_grafana_1
docker stop taiko_prometheus_1
docker stop taiko-taiko_client_proposer-1
docker stop taiko-grafana-1
docker stop taiko-prometheus-1

docker rm taiko-taiko_client_prover_relayer-1
docker rm taiko-taiko_client_driver-1
docker rm taiko-l2_execution_engine-1
docker rm taiko-zkevm_chain_prover_rpcd-1
docker rm taiko_grafana_1
docker rm taiko_prometheus_1
docker rm taiko-taiko_client_proposer-1
docker rm taiko-grafana-1
docker rm taiko-prometheus-1

docker volume rm taiko_grafana_data
docker volume rm taiko_l2_execution_engine_data
docker volume rm taiko_prometheus_data
docker volume rm taiko_zkevm_chain_prover_rpcd_data
```
