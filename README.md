# Windows ОС с нуля

Необходимый софт:
- https://www.chiark.greenend.org.uk/~sgtatham/putty/
- https://code.visualstudio.com/
- https://git-scm.com/



# Git первичная настройка на Windows

Пользователь
```bash
git config --global user.name "ars"
git config --global user.email ars.anosov@gmail.com
```

Т.к. Windows машина видит файлы по сети, изменение **прав** файлов заставляет Windows GIT думать что файлы модищицированы. Отключаем:
```bash
git config --global core.filemode false
```

Разные файлы могут иметь разные "концы" строк LF или CRLF. По умолчанию Git принудительно на Windows машине модифицирует файлы LF -> CRLF. Отключаем это "умное" поведение Git:
```bash
git config --global core.autocrlf false
```



# Новая Dev-машина

Win+X > "Приложения и возможности" > справа "Программы и компоненты"
- Включаю оснастку Hyper-V

Шпаргалка по установке необходимого DEV-софта на Linux-виртуалке:
- [Debian_11.md](Debian_11.md) - чистая машина, установка с нуля
- [Docker.md](Docker.md) - контейнеры без которых жизни нет

