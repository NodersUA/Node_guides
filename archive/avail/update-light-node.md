# Update (Light node)

```bash
systemctl stop avail_light
rm /usr/local/bin/avail_light
mkdir .avail/ && mkdir .avail/identity/
cp /root/avail-light/target/release/identity.toml /root/.avail/identity/
rm -rf avail-ligh .avail-light
```

```bash
sudo bash -c "cat > /root/.avail/availscript.sh" <<EOF
#!/bin/bash
# official script command of Avail script from daningyn
COMMAND="curl -sL1 avail.sh | bash"
# Here is script making LC restart if getting errors
while true; do echo "Starting command: \$COMMAND"
    # Run command in the background
    bash -c "\$COMMAND" &

    PID=\$!

    wait \$PID; EXIT_STATUS=\$?
    if [ \$EXIT_STATUS -eq 0 ]; then 
        echo "Command exited successfully. Restarting..."
    else 
        echo "Command failed with status \$EXIT_STATUS. Restarting..."
    fi

    sleep 10
done
EOF
```

```bash
chmod +x /root/.avail/availscript.sh
```

```bash
sudo bash -c "cat > /etc/systemd/system/avail_light.service" <<EOF
[Unit] 
Description=Avail Light Client
After=network.target
StartLimitIntervalSec=0
[Service] 
User=$USER 
ExecStart=/root/.avail/availscript.sh
Restart=always 
RestartSec=120
[Install] 
WantedBy=multi-user.target

EOF
```

```bash
systemctl daemon-reload
systemctl enable avail_light
systemctl restart avail_light && journalctl -u avail_light -f -o cat
```
