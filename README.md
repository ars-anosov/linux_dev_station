# Windows ОС с нуля
софт:
```powershell
Get-AppPackage -name *appinstaller* | select Name,Version

winget search chrome
winget install --id Google.Chrome --source winget
winget install --id Microsoft.PowerShell --source winget
winget install --id SublimeHQ.SublimeText.4 --source winget
winget install --id Termius.Termius --source winget
winget install --id Git.Git --source winget
winget install --id Microsoft.VisualStudioCode --source winget
winget install --id MongoDB.Compass.Community --source winget
winget install --id Discord.Discord --source winget
winget install --id SplitCam.SplitCam --source winget
```


## Git настройка на Windows
user:
```powershell
git config --global user.name "ars"
git config --global user.email ars.anosov@gmail.com
```

Т.к. Windows машина видит файлы по сети, изменение **прав** файлов заставляет Windows GIT думать что файлы модищицированы. Отключаем:
```powershell
git config --global core.filemode false
```

Разные файлы могут иметь разные "концы" строк LF или CRLF. По умолчанию Git принудительно на Windows машине модифицирует файлы LF -> CRLF. Отключаем это "умное" поведение Git:
```powershell
git config --global core.autocrlf false
```

## VSCode настройка на Windows
extensions:
- [SFTP](https://marketplace.visualstudio.com/items?itemName=Natizyskunk.sftp)



# Новая Dev-машина

Win+X > "Приложения и возможности" > справа "Программы и компоненты"
- Включаю оснастку Hyper-V

Шпаргалка по установке необходимого DEV-софта на Linux-виртуалке:
- [Debian_11.md](Debian_11.md) - чистая машина, установка с нуля
- [Docker.md](Docker.md) - контейнеры без которых жизни нет

