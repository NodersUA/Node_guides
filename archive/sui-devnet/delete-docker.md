# Delete (Docker)

```bash
docker rm sui_node -f && \
rm -rf $HOME/.sui /usr/bin/{sui,sui-node,sui-faucet} && \
docker rmi secord/sui
```
