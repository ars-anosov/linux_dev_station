# Debian 11

Использую официалный ISO - https://www.debian.org/index.ru.html

Работаю с виртуалкой IP=192.168.1.81.
- Правлю адрес в VSCode-плагине [sftp](.vscode/sftp.json)









# Базовая настройка под root

Чекаем версию, заходим в root с окружением root
```bash
cat /etc/os-release
# PRETTY_NAME="Debian GNU/Linux 11 (bullseye)"
su -
pwd
```


## Источники apt
- удаляем **deb cdrom:...**
- добавляем **main**, см. на debian.org [SourcesList](https://wiki.debian.org/ru/SourcesList)

```bash
vi /etc/apt/sources.list

#---
deb http://deb.debian.org/debian/ bullseye main
deb-src http://deb.debian.org/debian/ bullseye main

deb http://security.debian.org/debian-security bullseye-security main
deb-src http://security.debian.org/debian-security bullseye-security main

deb http://deb.debian.org/debian/ bullseye-updates main
deb-src http://deb.debian.org/debian/ bullseye-updates main
#---

apt update
```


## Время
```bash
timedatectl
ls -la /etc/localtime
timedatectl set-timezone Europe/Moscow
```


## sudo
```bash
apt install sudo
usermod -a -G sudo ars

#Перезахожу в ssh под ars
sudo whoami
```









# Сеть

Ухожу от DHCP
```bash
ip a
ip r
ip rule
sudo traceroute -d ya.ru

sudo vi /etc/network/interfaces
#---
auto eth0
allow-hotplug eth0
iface eth0 inet static
    address 192.168.1.81/24
    gateway 192.168.1.254
#---
```

Проверяем
```bash
sudo reboot
uptime
sudo systemctl list-unit-files
```









# Docker

См. [docs.docker.com](https://docs.docker.com/install/linux/docker-ce/debian/)

```bash
sudo apt-get remove docker docker-engine docker.io containerd runc
sudo apt-get update

sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
    $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

Проверяем: создаем тестовый контейнер
```bash
sudo docker run hello-world
ip a
sudo iptables-save
sudo docker ps -a
```

Машина разраба готва :)









# Автостарт

Система инициализации **Systemd** (*man systemd.service*), разжевано [тут](https://habr.com/ru/company/southbridge/blog/255845/).

Смотрим на какой юнит опираеммся:
```bash
sudo systemctl list-units | grep docker
```

Создаем юнит [container-nginx.service](files/systemd/container-nginx.service):
```bash
ls -la /etc/systemd/system/

sudo vi /etc/systemd/system/container-nginx.service
```

Стартуем юнит:
```bash
sudo systemctl status container-nginx
sudo systemctl enable container-nginx
sudo systemctl start container-nginx

sudo docker ps -a
```

Дебажим после reboot:
```bash
sudo journalctl -u container-nginx
```









# Файловый сервер Samba

Директория для расшаривания
```bash
mkdir ~/share
```

### Сервер
Подсматривал в [ubuntu](https://help.ubuntu.ru/wiki/%D1%80%D1%83%D0%BA%D0%BE%D0%B2%D0%BE%D0%B4%D1%81%D1%82%D0%B2%D0%BE_%D0%BF%D0%BE_ubuntu_server/%D1%81%D0%B5%D1%82%D1%8C_windows/samba_file_server)

```bash
sudo apt install samba
sudo vi /etc/samba/smb.conf
--------------------------------------------------
[share]
    comment = shared folder
    path = /home/user/share
    browsable = yes
    guest ok = no
    read only = no
    create mask = 0777
--------------------------------------------------
sudo service smbd restart
sudo service nmbd restart
```

### Пользователь
См. [help.ubuntu.ru](https://help.ubuntu.ru/wiki/samba)

Samba использует пользователей которые уже есть в системе. Надо пользователя в базу данных SMB и назначить пароль.
```bash
sudo smbpasswd -a user
```

Теперь необходимо включить этого пользователя.
```bash
sudo smbpasswd -e user
```