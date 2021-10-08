# Docker

Общий принцип:
- в каждом контейнере есть директория **/mnt**
- прокидываем директорию **~/share** с linux-виртуалки в директорию **/mnt** контейнеров

**! Копируем через sftp-плагин VSCode содержимое share на linux-виртуалку !**









# 1. MySQL (контейнер mysql-server:5.7)
см. [doc](https://hub.docker.com/_/mysql)

```bash
sudo docker run \
  --name mysql-57 \
  -d \
  --network=host \
  -v ~/share/mysql:/mnt/mysql \
  mysql/mysql-server:5.7
```

Создаем пользователя ***admin***
```bash
# смотрим пароль root
sudo docker logs mysql-57 2>&1 | grep GENERATED
# работаем с базой
sudo docker exec -it mysql-57 mysql -uroot -p
```

```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'rootpass';
CREATE USER 'admin'@'%' IDENTIFIED BY 'adminpass';
GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%';
FLUSH PRIVILEGES;
SELECT User, Host FROM mysql.user;
```

[Backup / Restore](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html)
```bash
sudo docker exec -it mysql-57 bash
# backup
mysqldump -uadmin -padminpass asterisk-dialplan > /mnt/backups/asterisk-dialplan/structure_and_data.sql
# restore
mysql -uadmin -padminpass asterisk-dialplan < /mnt/backups/asterisk-dialplan/structure_and_data.sql
```

GUI tool - [MySQL Workbench](https://dev.mysql.com/downloads/workbench/)

В 8-й версии возникнут проблемы import/export при работе с сервером версии 5.7. Нужен подходящий **mysqldump.exe**:
- Качаем [MySQL Community Server 5.7](https://dev.mysql.com/downloads/mysql/) - выковыриваем из архива mysqldump.exe
- MySQL Workbench: Edit > Preferences > Administration : Path to mysqldumptool - прописываем путь

выложил здесь [mysqldump-5-7-24.exe](files/mysql/mysqldump-5-7-24.exe)









# 3. nginx (контейнер nginx:stable)
см. [doc](https://hub.docker.com/_/nginx)

```bash
sudo docker run \
  --name nginx \
  -d \
  --network=host \
  -v ~/share/nginx/www:/mnt/www \
  -v ~/share/nginx/conf.d:/etc/nginx/conf.d \
  nginx:stable

sudo docker logs nginx
```

У себя на машине PowerShell (администратор):
```powershell
notepad C:\Windows\System32\drivers\etc\hosts
#---
192.168.1.81	site.home
#---

ping site.home
```









# 2. php-fpm (контейнер php-fpm-my:54)
см. [doc.](https://hub.docker.com/_/php/), 5.4 подсматривал у [johanvanhelden/dockerhero](https://hub.docker.com/r/johanvanhelden/dockerhero-php-5.4-fpm/dockerfile)

```bash
sudo docker images
sudo docker build -t php-fpm-my:54 ~/share/docker_images/php-fpm-54/

sudo docker run \
  --name php-fpm-54 \
  -d \
  --network=host \
  -v ~/share/nginx/www:/mnt/www \
  php-fpm-my:54

sudo docker logs php-fpm-54
```

Проверяем nxinx+php-fpm - http://site.home/index.php
