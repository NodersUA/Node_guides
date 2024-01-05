# Useful Commands

_**Useful Commands**_

```bash
# Restart docker
docker restart shardeum-dashboard
```

```bash
# Check docker logs
sudo docker logs shardeum-dashboard -fn100
```

Go to folder .shardeum

```bash
cd && cd .shardeum
```

Launching a shell

```bash
./shell.sh
```

To exit the shell, use the command

```bash
exit
```

Stop the node

```bash
operator-cli stop
```

Check node status

```bash
operator-cli status
```

Change the port through the terminal if necessary

```bash
operator-cli gui set port your_port_here
```

Start node

```
operator-cli start
```

```bash
operator-cli status | grep "state"
```

```bash
operator-cli stake 10
```
