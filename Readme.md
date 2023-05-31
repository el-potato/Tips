Несколько GIT учёток на одном компе
=================================

Предположим что у вас есть два рабочих каталога
* `c:\work`
* `c:\hobby`

в `c:\work` вы хотите коммитить с рабочего профиля гитхаб (`ololoev@job.org`), а в `c:\hobby` c личного (`info@ololoev.ru`)

для настройки необходимо перейти в домашнюю директрию текущего пользователя `cd ~/`
создать два коннфига

Файл `.gitconfig-work` с содержимым
```
[user]
	name = Ololosha Ololoev WORK GITHUB Profile
	email = ololoev@job.org
```
и

файл `.gitconfig-hobby` с содержимым
```
[user]
	name = Ololosha Ololoev Hobby GITHUB Profile
	email = info@ololoev.ru
```

и отредактировать файл `.gitconfig` сославшись на созданные конфиги для указанных дирректорий

```gitcongig
[user]
	name = Ololosha Ololoev Default GITHUB Profile
	email = info@ololoev.ru
[includeIf "gitdir/i:C:/work/**"]
	path = .gitconfig-work
[includeIf "gitdir/i:C:/hobby/**"]
	path = .gitconfig-hobby
```

Теперь если перейти в соответсвующий каталог и выполнить в терминале команды 
`git config --get user.name` или `git config --get user.email` вы увидите что с папкой связан именно тот пользователь, которого мы привязали в конфиге

> * Это не работает для пустых папок, работает только для репозиториев (наличие папки .git)
> * Это работает с версией git 2.13 и выше 


Несколько авторизованых GIT учеток на одном компе
---------------------------------------------
В терминале **git bash** перейти в каталог `cd ~\.ssh\` и выполнить команду
* для рабочей учетки
    ```bash
        ssh-keygen -t ed25519 -C "ololoev@job.org"
    ```
    На шаге где терминал запросит ввод названия пишем что-то осмысленное, например `ololoev_job`, далее ввести пароль и подтверждение пароля (можно оставить пустыми чтобы потом при каждом обращении к гитхабу не вводить пароль, но **это не так безопасно**)
    Теперь в терминале (git bash) пишем команду `clip < ~/.ssh/ololoev_job.pub` для копировани публично ключа в буфер обмена
    * Переходим в браузере на сайт https://github.com авторизуемся и заходим в настройки SSH keys https://github.com/settings/keys и добавляем новый ключ https://github.com/settings/ssh/new название любое например ololoev_job_work_comp а в поле key вставляем то, что было скопировано в буфер обмена командой  `clip < ~/.ssh/ololoev_job.pub` жмем зеленую кнопку

* для хобби учетки делаем по аналогии с рабочей:
    ```bash
        ssh-keygen -t ed25519 -C "info@ololoev.ru"
    ```
    называем например `ololoev_hobby` вводим или оставляем пустыми пароли, в терминале (git bash) пишем команду `clip < ~/.ssh/ololoev_hobby.pub` для копировани публично ключа в буфер обмена
    * Переходим в браузере на сайт https://github.com авторизуемся и заходим в настройки SSH keys https://github.com/settings/keys и добавляем новый ключ https://github.com/settings/ssh/new название любое например ololoev_hobby_work_comp а в поле key вставляем то, что было скопировано в буфер обмена командой  `clip < ~/.ssh/ololoev_hobby.pub` жмем зеленую кнопку

### Добавить конфиг ssh
идем в папку `cd ~\.ssh\` и добавляем файл без расширения с имененм `config`
в этом файле прописываем
```
Host github.com
  HostName github.com
  User OloloevGithubLogin
  IdentityFile ~/.ssh/ololoev_job
  IdentitiesOnly=yes
Host ololoev.ru
  HostName github.com
  User OloloevHobbyGithubLogin
  IdentityFile ~/.ssh/ololoev_hobby
  IdentitiesOnly=yes
```

### Проверяем
В терминале `git bash` вводим команду `ssh -T git@github.com` если в ответ с вами поздоровались, все хорошо, рабочая учетка работает.
Теперь вводим `ssh -T git@ololoev.ru` и убеждаемся что хобби учетка тоже работает.

Готово!

Обратите внимание что для хобби учетки надо клонировать репозитории с указанием ssh

`git@ololoev.ru:OloloevHobbyGithubLogin/training.git`


