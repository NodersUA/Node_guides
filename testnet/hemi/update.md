# Update

```bash
# Зупиняємо поточну службу
systemctl stop popmd

# Переміщуємо файл адреси для збереження
mv /root/heminetwork/bin/popm-address.json /root/heminetwork/popm-address.json

# Переходимо до каталогу з мережею
cd heminetwork

# Видаляємо старі файли
rm -rf hemi*

# Завантажуємо нову версію
curl -L -O https://github.com/hemilabs/heminetwork/releases/download/v0.10.0/heminetwork_v0.10.0_linux_amd64.tar.gz

# Створюємо директорію для нових файлів
mkdir -p hemi

# Розпаковуємо архів у створену директорію
tar --strip-components=1 -xzvf heminetwork_v0.10.0_linux_amd64.tar.gz -C hemi

# Видаляємо завантажений архів
rm heminetwork_v0.10.0_linux_amd64.tar.gz

# Відновлюємо файл адреси до нового місця
mv /root/heminetwork/popm-address.json /root/heminetwork/bin/popm-address.json

# Перезапускаємо службу та переглядаємо логи
systemctl restart popmd && journalctl -u popmd -f -o cat

```

