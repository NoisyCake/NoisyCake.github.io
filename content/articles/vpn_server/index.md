---
title: "Свой VPN для самых маленьких | VLESS | 3X-UI"
date: 2024-11-15 14:13:23
type: posts
tags:
  - vpn
draft: true
---

Дисклеймер: Материл носит исключительно информационный характер. Автор не призывает к каким-либо действиям.

Статья-гайд, посвящённая установке своего VPN-сервера на удалённом хостинге с помощью 3X-UI панели и протокола VLESS/XTLS-Reality

{{< admonition warning >}}
В процессе написания
{{< /admonition >}}
<!--more-->

1. Покупаем VPS/VDS. Порекомендовать какой-либо сервис не могу, разве что скажу, что сам пользуюсь [HSHP](https://hshp.host/virtual-servers), но он может затянуть с выдачей IP. Разумеется, при покупке нужно выбирать расположение сервера вне России. Нам хватит 1 ядра и 1гб RAM, желательно хотя бы 10гб на диске, ОС — Ubuntu 22.04 (для других дальнейшие инструкции могут не подойти).

<center>{{< image src="/images/VPS.png" >}}</center>

2. На своём пк открываем терминал/Bash/PowerShell от имени администратора. Пишем следующую команду `ssh root@ip_сервера`, где вместо "ip_сервера", соответственно, вставляем ip своего сервера. Терминал предложит ввести пароль, скорее всего при покупке сервис VPS самостоятельно сгенерировал его, ищем на почте или на странице купленной услуги.

{{< admonition info >}}
SSH — сетевой протокол, позволяющий управлять удалённой ОС
{{< /admonition >}}

<center>{{< image src="/images/ssh.png" >}}</center>

Если вы видите приглашение типа `root@host_name` — вы внутри, поздравляю! Теперь сменим сгенерированный root пароль на свой. Пишем команду `passwd`, вставляем новый пароль и подтверждаем его. 

{{< admonition info >}}
Вставка текста в терминал осуществляется либо клавишами `Ctrl + Shift + V`, либо правой кнопкой мыши (PowerShell)
{{< /admonition >}}

root — стандартное имя учётной записи, поэтому оно первоочерёдно попадает под брутфорс-атаки (перебор пароля). Обезопасим себя, создав нового пользователя и запретив вход для root (также называют суперпользователем). Пишем `adduser новое_имя`, придумываем сложный пароль и вбиваем/вставляем его. Для генерации сложных паролей я использую KeePass. Дальше жмём `Enter` вплоть до вопроса. Жмём `y`. Добавляем нового пользователя с правами администратора: `usermod -aG sudo имя_пользователя`, выходим из системы командой `exit`, либо закрывая терминал.

<center>{{< image src="/images/passwd.png" >}}</center>

Зайдём под именем созданного пользователя: `ssh имя_пользователя@ip_сервера`, и проверим работоспособность командой `sudo ls -la /root`. Вывод есть? Значит всё хорошо, идём дальше.

<center>{{< image src="/images/new_user.png" >}}</center>

Так, пора бы обновить систему. Пишем `sudo apt update && sudo apt full-upgrade -y` и ждём. 

Установим nano: `sudo apt install nano`, откроем конфиг SSH командой `sudo nano /etc/ssh/sshd_config` и изменим параметр "PermitRootLogin" с "yes" на "no".

{{< admonition info >}}
nano — консольный текстовый редактор Linux (UNIX-подобных ОС). Он покажется довольно специфичным юзеру, пользовавшемся только графическими редакторами, поэтому вот пару советов, которые могут вам помочь:
1. Передвигать курсор мышкой не получится, впрочем как и во всём терминале, так что используйте для этого стрелки на клавиатуре.
2. Нажмите `Ctrl + O -> Enter`, чтобы сохранить файл; Нажмите `Ctrl + X`, чтобы выйти из редактора.
{{< /admonition >}}

<center>{{< image src="/images/nano.png" >}}</center>

Так лучше, теперь root не сможет зайти в систему никоим образом. Ещё один важный шаг: нужно перевесить порт SSH с дефолтного на повыше. Открываем (если уже закрыли) тот же самый файл sshd_config, раскомментируем строку "Port", убрав `#` и пишем произвольный номер порта. Нужно запомнить его.

{{< admonition info >}}
IP — адрес сетевого устройства, порт — адрес сетевой службы/программы на этом IP
{{< /admonition >}}

<center>{{< image src="/images/nano2.png" >}}</center>

Не забываем перезапустить службу SSH: `sudo systemctl restart sshd`. Выйдем и зайдём нормально :D Подключаемся к серверу с нового порта: `ssh имя_пользователя@ip_сервера -p номер_порта`. 

Все предыдущие шаги были сделаны с целью обезопасить систему от непрошеных гостей. Сейчас же начнём установку **3X-UI** панели, которая облегчит настройку XRay и даст графический интерфейс. 

\- Так, стоп, а что за XRay?  
\- **XRay** — ПО, выполняющее функции прокси. Оно не является таковым в физическом смысле, но содержит протоколы, транспорты и всё нужное для работы прокси-сервера.

\- Окей, кажется ещё упоминались какие-то VLESS и Reality?
\- **VLESS** — протокол передачи данных, поддерживающий технологии маскировки, к примеру **XTLS-Reality**, позволяющий скрыть трафик под обычные HTTPS запросы.

\- Я запутался...
\- Давайте ещё разок с самого начала. VPS — виртуальный сервер, размещённый на физическом. В нашем случае он находится где-то в Германии. На нём установлена операционная система, вроде Windows, но не он, а дистрибутив (реализация) Linux, называемый Ubuntu. На эту ОС мы накатим 3X-UI — программу, которая облегчит настройку XRay — ПО для маршрутизации и проксирования трафика, использующее для этой цели протоколы по типу VLESS, а последний, в свою очередь, улучшает свой функционал, работая с технологией XTLS-Reality, которая позволит маскироваться под определённый сайт. Фух, ну с объяснениями вроде закончили...

Вернёмся к практической части. Выполняем последовательность следующих команд:
```bash
sudo apt install docker.io docker-compose
git clone https://github.com/MHSanaei/3x-ui.git
cd 3x-ui
sudo docker-compose up -d
```

Готово. Теперь переходим в браузер, в поисковой строке вбиваем http://ip_сервера:2053/panel/. В качестве логина и пароля вводим дефолтные admin/admin.

<center>{{< image src="/images/3x-ui.png" >}}</center>


