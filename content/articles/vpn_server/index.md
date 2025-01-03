---
title: "Свой VPN для самых маленьких | 3X-UI + VLESS"
date: 2024-12-30 20:00:02
type: posts
tags:
  - vpn
draft: false
---

<center>{{< image src="/images/feature.png" >}}</center>

Дисклеймер: Материл носит исключительно информационный характер. Автор не призывает к каким-либо действиям.

Статья-гайд, посвящённая установке своего VPN-сервера на удалённом хостинге с помощью 3X-UI панели и протокола VLESS/XTLS-Reality

<!--more-->

А вот и гайдик подъехал. Плюсы своего VPN-сервера мы обговаривали в [предыдущей статье](/articles/anonymity/), сейчас же пошагово разберёмся, как его настроить, также обсудим некоторую теоретическую часть по ходу установки.

Тут будет много работы с терминалом, поэтому желательно понимать, как взаимодействовать с этой сущностью. В Windows можно работать через PowerShell от имени администраторы, в macOS — через стандартный терминал. Если у вас Linux — вы и без меня всё знаете :)

## Аренда сервера
Покупаем VPS/VDS. Порекомендовать какой-либо сервис не могу, разве что скажу, что сам пользуюсь [HSHP](https://hshp.host/virtual-servers), но он может затянуть с выдачей IP. Разумеется, при покупке нужно выбирать расположение сервера вне России. Для личного пользования хватит 1 ядра и 1гб RAM, желательно хотя бы 10гб на диске, ОС — Ubuntu 22.04 (для других дальнейшие инструкции могут не подойти). Также важным фактором для нас является скорость передачи данных. По-хорошему нужно брать от 500мбит/сек, но для тестового показа я арендую дешёвенький VPS со 100мбит/сек.

<center>{{< image src="/images/VPS.png" >}}</center>

## Первое подключение к серверу
На своём пк открываем терминал/Bash/PowerShell от имени администратора. Пишем следующую команду `ssh root@ip_сервера`, где вместо "ip_сервера", соответственно, вставляем ip своего сервера. Терминал предложит ввести пароль, скорее всего при покупке сервис VPS самостоятельно сгенерировал его, ищем на почте или на странице купленной услуги.

{{< admonition info >}}
SSH — сетевой протокол, позволяющий удалённо управлять ОС
{{< /admonition >}}

<center>{{< image src="/images/ssh.png" >}}</center>

Если вы видите приглашение типа `root@host_name` — вы внутри, поздравляю! Да, тут нет привычного графического интерфейса как в обычном пк, но он и не нужен, ведь всё можно сделать через консоль. 

Теперь сменим сгенерированный root пароль на свой. Пишем команду `passwd`, вставляем новый пароль и подтверждаем его. 

{{< admonition info >}}
Вставка текста в терминал осуществляется либо клавишами `Ctrl + Shift + V`, либо правой кнопкой мыши (PowerShell/PuTTY)
{{< /admonition >}}

## Защита сервера
root — стандартная учётная запись администратора, которая чаще других становится целью брутфорс-атак (перебора паролей). Обезопасим себя, создав нового пользователя и запретив вход для root (также называемого суперпользователем). Пишем `adduser новое_имя`, придумываем сложный пароль и вбиваем/вставляем его. Для генерации сильных паролей я использую KeePass. Дальше нажимаем `Enter` несколько раз, пока не появится вопрос. Жмём `y`. Добавляем нового пользователя с правами администратора: `usermod -aG sudo имя_пользователя`, выходим из системы командой `exit` либо закрывая терминал.

<center>{{< image src="/images/passwd.png" >}}</center>

Зайдём под именем созданного пользователя: `ssh имя_пользователя@ip_сервера`, и проверим работоспособность командой `sudo ls -la /root`. Вывод есть? Значит всё хорошо, идём дальше.

Теперь перед каждой командой, требующей доступ администратора, нам придётся писать `sudo` и вводить пароль (единоразово для сессии), ведь мы работаем не через суперпользователя, имеющего доступ ко всему и вся.

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

{{< admonition info >}}
Если вам надоело, что соединение с сервером теряется после нескольких минут бездействия, измените значения параметров `ClientAliveInterval` и/или `ClientAliveCountMax` на более высокие. У меня стоят 300 и 12 соответственно
{{< /admonition >}}

Не забываем перезапустить службу SSH: `sudo systemctl restart sshd`.  
Выйдем и зайдём нормально :D  
Прописываем `exit`, затем подключаемся к серверу с нового порта: `ssh имя_пользователя@ip_сервера -p номер_порта`. 

Все предыдущие шаги были сделаны с целью обезопасить систему от непрошеных гостей. Сейчас же начнём установку **3X-UI** панели, которая облегчит настройку XRay и даст графический интерфейс. 

> \- Так, стоп, а что за XRay?  
\- **XRay** — ПО, выполняющее функции прокси. Оно не является таковым в физическом смысле, но содержит протоколы, транспорты и всё нужное для работы прокси-сервера.

> \- Окей, кажется ещё упоминались какие-то VLESS и Reality?  
\- **VLESS** — протокол передачи данных, поддерживающий технологии маскировки, к примеру **XTLS-Reality**, позволяющий скрыть трафик под обычные HTTPS запросы.

> \- Я запутался...  
\- Давайте ещё разок с самого начала. VPS — виртуальный сервер, размещённый на физическом. В нашем случае он находится где-то в Германии. На нём установлена операционная система, вроде Windows, но не он, а дистрибутив (реализация) Linux, называемый Ubuntu. На эту ОС мы накатим 3X-UI — программу, которая облегчит настройку XRay — ПО для маршрутизации и проксирования трафика, использующее для этой цели протоколы по типу VLESS, а последний, в свою очередь, улучшает свой функционал, работая с технологией XTLS-Reality, которая позволит маскироваться под определённый сайт. Фух, ну с объяснениями вроде закончили...

## Установка 3x-ui
Вернёмся к практической части. Выполняем последовательность следующих команд:
```bash
sudo apt install docker.io docker-compose
git clone https://github.com/MHSanaei/3x-ui.git
cd 3x-ui
sudo docker-compose up -d
```

Готово. Теперь переходим в браузер, в поисковой строке вбиваем `http://ip_сервера:2053/panel/`. В качестве логина и пароля вводим дефолтные admin/admin.

<center>{{< image src="/images/3x-ui.png" >}}</center>

## Защита панели
Хорошо, панель есть. Теперь нужно защитить её от тех же брутфорс-атак. Для этого переходим в настройки и:

1. Меняем корневой путь URL адреса. У меня будет `/kekw/` (запишите его).

2. Меняем порт панели. Желательно выбрать в пределах от 60000 до 65535 (также запомните или запишите).

<center>{{< image src="/images/settings1.png" >}}</center>

{{< admonition info >}}
Чтобы изменить язык на русский, выберите страну в настройках в поле "Language"
{{< /admonition >}}

Сохраните настройки и перезапустите панель. Теперь вы сможете войти в неё по следующему URL: `http://ip_сервера:указанный_порт/корневой_путь/`

3. На странице "Настройки безопасности" измените стандартные логин и пароль.

<center>{{< image src="/images/settings2.png" >}}</center>

## Настройка VLESS
Теперь, наконец-таки, займёмся подключением протокола! Переходим во вкладку "Подключения" (Inbounds) и жмём плюсик. 

В "Примечании" (Remark) пишем любое название подключения, протокол — VLESS, "Порт IP" (Listening IP) — пустой, порт — 443.

<center>{{< image src="/images/vless1.png" >}}</center>

В настройках клиента меняем Email (по факту никнейм) на что-то уникальное и удобочитаемое. ID сгенерировано панелью, общий расход и дату окончания устанавливаем по усмотрению, я же оставлю эти поля пустыми. 

<center>{{< image src="/images/vless2.png" >}}</center>

Ниже в строке "Безопасность" выбираем "Reality", после чего в настройках клиента появляется строка "Flow". В выпадающем окне жмём на "xtls-rprx-vision".

Возвращаемся к настройке транспорта. В "uTLS" выбираем chrome. В "Dest" прописываем адрес сайта, по которому будет перенаправляться подключения при маскировке трафика. Я выберу google.com:443 (порт оставляем 443, чтобы подключаться по https). В "SNI" указываем "google.com,www.google.com" — адреса, под которые будет маскироваться трафик, переадресовывая на "Dest".  
Короче говоря теперь провайдер будет думать, что вы подключаетесь к google.com, даже если вы находитесь на nelzagram.com.

Чтобы помочь серверу корректно обработать разные типы трафика, включите "Sniffing".

<center>{{< image src="/images/vless3.png" >}}</center>

## Подключение к VPN
Готово! Осталось только подключиться. Для этого воспользуемся каким-либо из клиентов. Для любых устройств я рекомендую [Hiddify](https://github.com/hiddify/hiddify-app). Чтобы скачать его, перейдите по ссылке или найдите репозиторий в поисковике, промотайте вниз до "Direct Download" и выберите свою платформу.

Вернёмся к панельке. Сейчас имеем такую ситуацию:

<center>{{< image src="/images/inbound1.png" >}}</center>

Жмём на плюсик рядом с "ID", выпадут текущие пользователи. Тыкаем на значок qr-кода, затем на сам высветившийся qr — ссылка будет скопирована в буфер обмена.

<center>{{< image src="/images/inbound1.png" >}}</center>

Теперь откроем Hiddify. Жмём на плюсик в правом верхнем углу, далее на "Добавить из буфера обмена". Осталось нажать на центральную кнопку.. та-дам! Вот и готов наш собственный VPN!  

<center>{{< image src="/images/hiddify.png" >}}</center>

В параметрах конфигурации вы можете поставить регион ru, чтобы подключаться к сайтам с доменами `.ru` напрямую — так шанс идентификации использования VPN приближается к нулю. Если же всякие нельзяграммы так и не работают, измените в настройках режим работы на "VPN". Учтите, что в этом случае нужно будет открыть приложение с правами администратора. У меня, к примеру, по дефолту режим стоял "Прокси".

<center>{{< image src="/images/hiddify2.png" >}}</center>

Схема подключения на мобильных устройствах практически идентична. Ссылку можете получить, отсканировав qr или переслав через тот же телеграм. Также нужно выбрать режим работы VPN и установить регион по желанию. К слову, я использую VPN для всех сайтов (регион — Другой), и за полтора месяца проблем не было.

<center>{{< image src="/images/hiddify_android.png" >}}</center>

### Пользователи
Для каждого подключения есть возможность создать несколько пользователей. Для чего? Ну можно следить за расходом трафика, ограничивать его, задавать начальную и окончательную даты использования и может что-то ещё... 

Создать новых пользователей очень просто. Ищем наше подключение, нажимаем на значок <i class="fa-solid fa-ellipsis-vertical"></i> под "Меню" -> "<i class="fa-solid fa-user-plus"></i> Добавить пользователя". В качестве Email пишем уникальное имя, а в качестве Flow устанавливаем тот же "xtls-rprx-vision".

<center>{{< image src="/images/add_user.png" >}}</center>

Супер, теперь у нас есть 2 пользователя. Ко второму может подключиться, например, наш друг. Просто берём ссылку, нажав на соответствующий qr в строке пользователя.

<center>{{< image src="/images/add_user2.png" >}}</center>

{{< admonition warning >}}
Не давайте доступ к VPN слишком большому количеству людей, поскольку они могут перегрузить сервер по памяти или пропускной способности интернета при одновременном активном пользовании. Для скорости в 500мбит я бы ограничился 6-8 людьми.
{{< /admonition >}}

## Установка сертификата
Вверху панели вы наверняка заметили предупреждение о том, что соединение не защищено. Так и есть, ведь мы подключаемся к этой панели по протоколу http, а вот чтобы подключаться по https, нам нужен SSL/TLS сертификат. 

Вообще сертификаты TLS подписываются специализированными центрами, но мне такой вариант не очень нравится, так как он требует наличия домена, а это либо затраты + раскрытие своих паспортных данных, либо ненадёжные бесплатные поддомены, так что обучу вас тайной технике самоподписи...

Будем пользоваться утилитой OpenSSL. Снова подключаемся к нашему серверу: `ssh имя_пользователя@45.132.1.46 -p 37140` -> вводим пароль. Скачиваем пакеты: `sudo apt install openssl` -> снова вводим пароль.

Перейдём в папку, куда устанавливали панель и создадим там директорию, где будут храниться ключи:
```bash
cd 3x-ui
mkdir cert
cd cert
```

Теперь создадим приватный ключ командой `openssl genrsa -out private.key 2048`, где вместо "private" вы можете вставить любое имя для ключа. 

Далее, создаём "публичный ключ", то есть подписываем сертификат: `openssl req -key private.key -new -out public.csr`, где вместо "public" вы также можете вставить любое имя. Сейчас у нас будут спрашивать информацию о нашем сайте / компании, которую он представляет. Оставляем всё по умолчанию, нажимая `Enter`, КРОМЕ пункта "Common Name", где вписываем ip VPS сервера.  
Пишем `openssl x509 -signkey private.key -in public.csr -req -days 3650 -out public.crt`, где вместо "3650" вы можете вставить любое число дней, на которое хотите подписать сертификат. По истечении срока вам снова придётся его подписать или создать новый.

Посмотрим, что имеется в папке cert:

<center>{{< image src="/images/openssl.png" >}}</center>

Отлично, все файлы на месте.  
Открываем панельку и бежим в настройки. В поле "Путь к файлу публичного ключа сертификата панели" вводим `/root/cert/public.crt`, а в поле "Путь к файлу приватного ключа сертификата панели" вводим `/root/cert/private.key`. Нажимаем "Сохранить" в верхней левой части. 

> Если у вас выскакивает ошибка по типу:
>
> {{< admonition error >}}
Изменение настроек Неудачно: cert file </root/cert/public.crt> or key file </root/cert/private.key> invalid: open /roеot/cert/public.crt: no such file or directory
{{< /admonition >}}
>
> то попробуйте следующее: перейдите в папку "3x-ui", откройте файл конфигурации: `sudo nano docker-compose.yml` и измените строчку `- $PWD/cert/:/root/cert/` на `- /home/имя_пользователя/3x-ui/cert/:/root/cert/`. 
>
> <center>{{< image src="/images/docker-compose.png" >}}</center>
>
> Перезапустите docker и попробуйте сохранить изменения в панели снова:
> ```bash
> sudo docker-compose down
> sudo docker-compose up -d
> ```

Сохранилось? Ну и славно. Перезапускаем панель и видим предупреждение браузера:

<center>{{< image src="/images/browser-warning.png" >}}</center>

Это нормально, просто хром в курсе, что сертификат самоподписанный, но нехотя установит https соединение, если мы всё таки перейдём по ссылке.

Ура! Красное предупреждение исчезло 🥳

## Telegram бот
Интересная фича, которая позволит вам управлять панелью через Telegram бота. Делается всё очень и очень просто.

Для начала переходим к [@BotFather](https://t.me/BotFather), стартуем -> /newbot -> Пишем любое имя -> Пишем юзернейм с "bot" на конце -> Копируем токен и вставляем его в панели в соответствующую строку в настройках Telegram бота. Сохраняем изменения и перезапускаем панель.

<center>{{< image src="/images/bot.png" >}}</center>

Перейдём по ссылке к боту. Получаем свой id командой, как ни странно, `/id`, и также вставляем в панель, снова перезапуская её. Теперь бот знает, что вы — ~~хозяин~~ админ.

<center>{{< image src="/images/bot2.png" >}}</center>

Панель сейчас выглядит как-то так:

<center>{{< image src="/images/bot3.png" >}}</center>

По желанию вы можете настроить и другие параметры, например, частоту отправки уведомлений или язык бота. 

Так, хорошо, посмотрим, что умеет бот. Снова пишем `/start` — выпадает менюшка. Видим: Использование сервера, бэкапы баз данных, настройки подключений и их пользователей, просмотр онлайн клиентов и т.п. Также без подключения к самой панели через бота можно перезапускать ядро командой `/restart force`. 

<center>{{< image src="/images/bot4.png" >}}</center>

Вы можете сделать так, чтобы другие пользователи тоже смогли просматривать объём используемого трафика. Для этого следуйте "All Clients" -> "имя_подключения" -> "имя_пользователя" -> "Set Telegram User" -> "Select Telegram User". Теперь пользователь вашего VPN может просматривать свою информацию (но не более того).

<center>{{< image src="/images/bot5.png" >}}</center>

Поздравляю, бот готов!

## Подписки
Тоже довольно интересная функция, позволяющая направить создание своего VPN в коммерческий вектор. С помощью подписки можно создать ссылку сразу на несколько подключений, то есть у пользователя появится сразу несколько конфигураций VPN в клиенте, к тому же, изменения будут подтягиваться автоматически (без подписки придётся удалять и создавать подключения в клиенте заново). Вдруг какой-то перестанет работать, а тут второй как раз под рукой. Прикольнее это будет выглядеть вкупе с [Hiddify Manager]()(статья будет позже), но про него не сегодня. 

Чтобы активировать эту функцию, переходим в настройки панели -> "Подписка" и включаем службу. Пути к файлам сертификатов выбираем те же, что делали для самой панели, корневой путь подписки меняем на произвольный. Порт выставляем какой-нибудь другой, но он не должен использоваться другими службами на вашем VPS. Чтобы проверить прослушиваемые порты, вбейте `ss -ntlp`. Выбираем любой четырёх/пятизначный НЕ из этого списка.

<center>{{< image src="/images/ss.png" >}}</center>

На всякий случай откроем выбранный порт на сервере:
```bash
sudo apt install ufw
ufw allow выбранный_порт
```

Можно также изменить URI путь в подписке json, чтобы избавиться от предупреждения.

Сохраняем конфиг и перезапускаем панельку. Получили что-то подобное:

<center>{{< image src="/images/subscription.png" >}}</center>

Теперь можем подключать клиентов к подписке. Для этого идём в подключения -> "<i class="fa-solid fa-pen"></i> Редактировать пользователя". В выпашем окне в поле "Subscription" пишем название подписки. Это же название вы будете писать каждый раз, когда хотите присоединить клиента в другом подключении к этой подписке. Сохраняем изменения.

<center>{{< image src="/images/subscription2.png" >}}</center>

Для примера я сделал второе подключение, впишем идентичное название подписки для его клиента.

А теперь плохая новость: Hiddify не умеет работать с подписками 3x-ui, поэтому скачаем другой клиент. Я буду использовать [NekoRay](https://github.com/Matsuridayo/nekoray/releases). Немного мотаем вниз и качаем версию для Windows (ну или какой у вас там хост) -> разархивируем -> запускаем "nekobox.exe". 

Включаем "Режим TUN" в правом верхнем углу — он позволит работать в режиме VPN.  
Поскольку наш сертификат самоподписанный, мы проигнорируем ошибки, связанные с его проверкой. Настройки -> Основные настройки -> Подписка -> Ставим галочку на "Игнорировать ошибки TLS при обновлении подписок".

{{< admonition warning >}}
Никогда так не делайте, если не доверяете источнику прокси. Коммерческие хостинги должны получать сертификаты, подписанные центрами, а если это не так — шлите в пень. Также не отключайте предупреждения, если вы уже используете не свои прокси или планируете это делать.
{{< /admonition >}}

Переходим в 3x-ui, выбираем подключение и клиента, которому присвоили подписку, жмём на значок qr, затем на qr-код ПОДПИСКИ. Возвращаемся в NekoBox (NekoRay) -> Ctrl + V -> OK. В журнале нам говорят, что два профиля были импортированы. Можно подключиться к каждому из них.

<center>{{< image src="/images/nekoray.png" >}}</center>

Для мобилок можно использовать тот же [NekoBox](https://github.com/Matsuridayo/NekoBoxForAndroid/releases). Выбираем версию для arm64, качаем и устанавливаем апк. Аналогично копируем ссылку -> значок плюса -> "Импорт из буфера обмена" -> центральная внизу.

Вот и всё. Теперь изменения, совершённые в панели, клиент будет сам доставать в течение установленного времени.

Гайд подошёл к концу! Если у вас возникнут трудности/вопросы/предложения, жду их в [лс](https://t.me/noisycake)! Спасибо, что читаете!

Ну и конечно, с наступающим Новым Годом! 🎅🎁