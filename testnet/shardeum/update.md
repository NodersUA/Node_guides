# Update

Go to folder .shardeum

```bash
cd && cd .shardeum
```

Launching a shell

```bash
./shell.sh
```

```bash
operator-cli status | grep "state"
```

If state is "waiting-for-network" or "standby":

```bash
exit
```

```bash
cd $HOME && curl -O https://gitlab.com/shardeum/validator/dashboard/-/raw/main/installer.sh && chmod +x installer.sh && ./installer.sh
```

Go to folder .shardeum

```bash
cd && cd .shardeum
```

Launching a shell

```bash
./shell.sh
```

Start node

```
operator-cli start
```

```bash
operator-cli status | grep "state"
```

```
cd
```
