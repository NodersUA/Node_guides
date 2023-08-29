# Delete

```bash
sudo systemctl stop defundd df_defund ar_defund && \
sudo systemctl disable defundd df_defund ar_defund && \
rm /etc/systemd/system/defundd.service && \
rm /etc/systemd/system/df_defund.service && \
rm /etc/systemd/system/ar_defund.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf defund && \
rm -rf .defund && \
rm -rf df_defund && \
rm -rf $(which defundd) && \
rm ~/scripts/ar_defund.sh
```
