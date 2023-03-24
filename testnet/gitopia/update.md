**Update**

Используем скрипт для автоматического обновления
```bash
curl https://get.gitopia.com/ | bash
```

Перезапускаем ноду и проверяем логи
```bash
systemctl restart gitopiad && journalctl -u gitopiad -f -o cat
```
