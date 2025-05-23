---
title: "VPN на домашнем роутере | Xiaomi AX3000T"
date: 2025-01-26 00:00:01
type: posts
tags:
  - vpn
weight: 2
draft: false
---

<center>{{< image src="/images/feature.png" >}}</center>

Дисклеймер: Материл носит исключительно информационный характер. Автор не призывает к каким-либо действиям.

Статья-гайд, посвящённая перепрошивке и подключении к прокси наиболее популярного роутера под VPN на данный момент.

<!--more-->
---
Привет! Вы просили, мы старались :)  
Сегодня посмотрим, как поставить VPN на роутер на примере довольно бюджетного Xiaomi AX3000T, причём с раздельным проксированием, то есть вы сможете выбрать, на какие именно сайты нужно ходить через прокси.

Я постарался сделать инструкцию максимально простой, ведь самую мучительную часть мы выполним одним готовым скриптом, но если вам будет интересно, в дальнейшем можем разобрать и ручную настройку.  
Кроме того, хоть конкретика относится только к упомянутому роутеру, саму суть можно применить к любому другому, который поддерживает перепрошивку под OpenWRT, поэтому если у вас уже есть какой-то маршрутизатор дома, проверьте в тырнете ([клик](https://openwrt.org/ru/supported_devices)) его потенциал в этом направлении, вдруг повезёт. 

## Вопрос-ответ
>\- Зачем нужен VPN на роутере?  
>\- К примеру, можно будет смотреть ютуб на телевизоре, который медленно работает из-за "устаревания оборудования серверов". Также кому-то может оказаться очень удобным подключаться к WI-FI и заходить на сайты, ограниченные для пользователей из РФ (и только 😉) без включения VPN непосредственно на устройстве.

>\- OpenWRT — это что?  
>\- Бесплатная ОС на основе Linux для роутеров. Перепрошив ей свой маршрутизатор, вы разблокируете все его возможности вплоть до установки готовой конфигурации VPN.

>\- А sing-box — что такое?  
>\- Универсальная прокси-платформа по типу Xray, с помощью которой создаются прокси-сервера с различными протоколами.

>\- Зачем роутеры создают две сети?  
>\- Сегодня большинство даже бюджетных роутеров являются двухдиапазонными, т.е. работают на двух частотах: 2.4 ГГц и 5 ГГц. Для каждой частоты нужно своё соединение. Раньше использовали только 2.4 ГГц, но со временем куча роутеров, работающих на одной частоте, начали перебивать сигналы друг друга, что снижало скорость соединения. Для решения проблемы придумали 5 ГГц. Особенностью последней частоты является более высокая скорость, но в то же время она покрывает меньшую площадь. 

>\- Я закирпичил роутер...  
>\- Без паники, скорее всего не всё потеряно. Переходите в раздел [Восстановление роутера](#%d0%b2%d0%be%d1%81%d1%81%d1%82%d0%b0%d0%bd%d0%be%d0%b2%d0%bb%d0%b5%d0%bd%d0%b8%d0%b5-%d1%80%d0%be%d1%83%d1%82%d0%b5%d1%80%d0%b0)

>\- Ничего не получается.. что делать?  
>\- Лучшим решением будет просмотреть соответствующие ветки форумов: русскоязычный [4PDA](https://4pda.to/forum/index.php?showtopic=1074874) и англоязычный [OpenWRT](https://forum.openwrt.org/t/openwrt-support-for-xiaomi-ax3000t/180490). Либо напишите мне в [тг](https://t.me/noisycake), постараюсь помочь :)

## Что понадобится
1. Роутер, поддерживающий перепрошивку под OpenWRT
2. VPS сервер с 3X-UI на нём. Если ещё не обзавелись этим чудом, читайте [предыдущий гайд](https://noisycake.ru/articles/vpn_server/)
3. Другой роутер, который наверняка стоит у вас сейчас и раздаёт интернетные синусоиды, если нет, то просто кабель от провайдера

Приступим!

{{< admonition danger >}}
Все действия вы выполняете на свой страх и риск! Я не несу ответственности за них, а лишь преподношу информацию. Перепрошивать или нет — решать вам
{{< /admonition >}}

## Авторизация в сети
Первым делом нужно включить интернет на родной прошивке. Для этого патч-кордом, который лежал в коробке (ну или любым другим), соединяем WAN-порт роутера (ближайший порт к питанию — отмечен на картинке ниже) с пк. Не забываем воткнуть кабель провайдера (или соединить с другим роутером) в любой другой порт. Включаем роутер в розетку и ждём, пока не замигает оранжевый диод.

<center>{{< image src="/images/router1.png" >}}</center>

Смотрим на доступные сети. Нам нужен `rd23_minet_bbc3`. Ладно, можете тихо улыбнуться... 

<center>{{< image src="/images/MiNet.png" >}}</center>

В браузере у нас должна открыться следующая вкладка. Если нет, вбейте в поисковую строку `router.miwifi.com`. 

{{< admonition info >}}
Если прошивка роутера глобальная, интерфейс будет на английском языке, иначе — на китайском. В первом случае вам потребуется выбрать ваш регион по кнопке "Click to select" в нижней части экрана
{{< /admonition >}}

<center>{{< image src="/images/miwifi1.png" >}}</center>

Ставим галочки и жмём большую синюю кнопку. 

В следующем окне нам нужно прописать пароль администратора для входа в дефолтную панель управления. Неважно какой, поскольку мы будем перепрошивать, поэтому вбиваем что-то максимально простое и соответствующее критериям.

<center>{{< image src="/images/miwifi2.png" >}}</center>

Название сети можем оставить по умолчанию, пароль также делаем самым простеньким. Опять же, позже всё равно придётся менять.  
Галочку с автоматическим обновление прошивки (первая) лучше убрать!

<center>{{< image src="/images/miwifi3.png" >}}</center>

Если всё получилось, мы увидим такое окошко с характеристиками нашего подключения. Можете оставить эту вкладку, чтобы подсмотреть пароли при необходимости.

<center>{{< image src="/images/miwifi4.png" >}}</center>

Интернет готов, можем проверить это, подключившись к созданной сети и зайдя на любой сайт.

## Уточнение деталей
И сразу напасть: версий прошивки Xiaomi AX3000T бывает несколько, и с каждой действия могут немного отличаться. Более того, в разных роутерах стоят разные чипы памяти и свитчи, от которых тоже зависит процесс перепрошивки. Так что же делать? 

Для начала нам нужно узнать, какая память стоит в роутере. Смотрим на картинку и светим в отмеченное место фонариком. Попытайтесь разглядеть, что написано на небольшом чёрном чипе. У меня Winbond.

<center>{{< image src="/images/router2.png" >}}</center>

Теперь заходим в панель управления по адресу 192.168.31.1. Пишем админский пароль, установленный ранее.

<center>{{< image src="/images/miwifi5.png" >}}</center>

Игнорируем уведомление об обновлении, если таково присутствует. Смотрим текущую версию прошивки, которая указана в левой части экрана (отмечено на скрине далее). 

<center>{{< image src="/images/miwifi6.png" >}}</center>

Самую актуальную информацию по установке прошивки на разные версии и чипы можно найти на [официальном сайте OpenWRT](https://openwrt.org/inbox/toh/xiaomi/ax3000t), особое внимание обращайте на предупреждения вверху страницы. <u>На момент написания статьи</u> особенности такие:

1. **1.0.31**. Рекомендуется сначала обновиться до 1.0.49. Как это сделать, рассказано [здесь](#%d0%b2%d0%be%d1%81%d1%81%d1%82%d0%b0%d0%bd%d0%be%d0%b2%d0%bb%d0%b5%d0%bd%d0%b8%d0%b5-%d1%80%d0%be%d1%83%d1%82%d0%b5%d1%80%d0%b0). Да, параграф про восстановление, но вы можете скачать официальную прошивку 1.0.49 и установить её по описанной инструкции. 

    {{< admonition info >}}
**ВАЖНО!** Вообще я успешно перепрошил 1.0.31 без обновления, поэтому можете пропустить этот шаг
    {{< /admonition >}}

2. **1.0.90 и выше** — лучше откатиться на более старую 1.0.49 по тому же пункту ["Восстановление"](#%d0%b2%d0%be%d1%81%d1%81%d1%82%d0%b0%d0%bd%d0%be%d0%b2%d0%bb%d0%b5%d0%bd%d0%b8%d0%b5-%d1%80%d0%be%d1%83%d1%82%d0%b5%d1%80%d0%b0), потому что мне <u>не удалось</u> прошить тем способом, о котором дальше будет идти речь.

2. **Чип Winbond или Foresee**. Рекомендуется использовать OpenWRT версии 24.10.X

3. **Чип ESMT**. Определённо OpenWRT версии 23.05.X.

По сути для большинства случаев сейчас есть универсальное решение — кастомная прошивка, которая учитывает тип свитча, памяти и версии. Но на счёт чипа я всё же рекомендую ориентироваться на документацию OpenWRT, то есть будем качать версию прошивки, основываясь на чипе памяти:

<center>{{< image src="/images/hardwares.png" >}}</center>

## Перепрошивка
И-так, как я уже упомянул, у меня чип Winbond, поэтому я установлю версию OpenWRT 24.10. Качать будем кастомную прошивку [от remittor](https://github.com/openwrt-xiaomi/builder/releases) (он же acdev на 4PDA). Выбираем архив для AX3000T нужной версии, качаем на пк и разархивируем в какую-нибудь папку.

<center>{{< image src="/images/custom_firmware.png" >}}</center>

Качаем [Xmir-patcher](https://github.com/openwrt-xiaomi/xmir-patcher) — прошивальщик роутеров Xiaomi от того же remittor. Для этого нажимаем на зелёную кнопку `Code` -> `Download ZIP`. Также разархивируем.

<center>{{< image src="/images/xmir-patcher1.png" >}}</center>

Берём файл с расширением **UBI** из нашей прошивки и перемещаем его в папку `xmir-patcher-main/firmware`

<center>{{< image src="/images/xmir-patcher2.png" >}}</center>

Если у вас не установлен интерпретатор Python, нужно это сделать. Для Windows: заходим на официальный сайт, качаем последнюю версию и запускаем. Отметьте галочками пункт "Add python.exe to PATH" и установите.

<center>{{< image src="/images/python.png" >}}</center>

Возвращаемся в xmir-patcher и запускаем, открывая `run.bat`. Не пугаемся командой строки! Здесь всё просто. Для начала сверимся, что IP-адрес в первом пункте совпадает с IP нашего роутера. Если нет, пишем "1" -> `Enter` -> указываем верный IP. С вероятностью 99,9% у вас, как и у меня, 192.168.31.1.

<center>{{< image src="/images/xmir-patcher3.png" >}}</center>

Теперь пишем "2" -> `Enter` — запустится установка эксплоита на роутер. В терминале должно отобразиться следующее:

<center>{{< image src="/images/xmir-patcher4.png" >}}</center>

По окончании процесса нажмите "7" -> `Enter`. Немного ждём...  
Если вы видите строки в конце по типу
```bash
The firmware has been successfully flashed!
Send command "reboot" via SSH/Telnet ...
Forced REBOOT Activated!
```
...поздравляю, у вас всё получилось! Роутер перепрошит :)

## Настройка OpenWRT
После перепрошивки у нас появятся два соединения со следующими названиями:

<center>{{< image src="/images/new_connections.png" >}}</center>

Подключаемся к любому из них с паролем `12345678`.

Переходим по адресу 192.168.1.1 и попадаем в панель управления прошивкой:

<center>{{< image src="/images/openwrt1.png" >}}</center>

Жмём логин. Нас встречает информация о роутере, соединении и т.п. Первое, что нужно сделать — это обновиться. Для этого жмём на синюю кнопку во втором предупреждении сверху "Go to firmware upgrade..."

<center>{{< image src="/images/openwrt2.png" >}}</center>

Мотаем вниз и жмём "Flash image" -> "Browse" -> Выбираем файл со скаченной прошивки с названием `openwrt-24.10.0-rc5-250113-xiaomi_mi-router-ax3000t-sysupgrade.bin` -> "Upload".

<center>{{< image src="/images/openwrt3.png" >}}</center>

Убираем первую галочку -> "Continue" и ждём окончания обновления.

<center>{{< image src="/images/openwrt4.png" >}}</center>

Как только сети появились, подключаемся к ним снова.

{{< admonition warning >}}
Если ваш пк автоматически подключается к другой WI-FI сети, переподключайтесь обратно к сети роутера Xiaomi AX3000T, иначе зайти в панель не удастся
{{< /admonition >}}

Сменим root-пароль. Для этого тыкаем на другую синюю кнопку в оставшемся предупреждении. В идеале придумайте сложный пароль и запишите его. Для таких целей лично я пользуюсь KeePass.

<center>{{< image src="/images/openwrt5.png" >}}</center>

Можно также настроить имена и пароли WI-FI сетей: идём в "Network" -> "Wireless" -> "Edit" на любой ячейке, начинающейся с "SSID". Устанавливаем нужные параметры и нажимаем "Save" (пароль настраивается в "Wireless Security"). Повторяем эти действия для второй сети.

{{< admonition info >}}
Чтобы получить одну сеть из двух, поставьте им одинаковые "ESSID" в настройках
{{< /admonition >}}

<center>{{< image src="/images/openwrt6.png" >}}</center>

<center>{{< image src="/images/openwrt7.png" >}}</center>

## Настройка маршрутизации
Осталось совсем немного. Открываем командную строку, PowerShell, MobaXterm или любую другую программу для подключения по SSH.

{{< admonition warning >}}
На этом этапе воткните провод от провайдера в WAN-порт роутера (ближайший к питанию)
{{< /admonition >}}

Пишем `ssh root@192.168.1.1` -> `yes` -> вставляем придуманный ранее пароль для root правой кнопкой мыши. 

<center>{{< image src="/images/ssh.png" >}}</center>

Настраивать это дело будем автоматически с помощью [скрипта от itdog](https://itdog.info/tochechnaya-marshrutizaciya-po-domenam-na-routere-s-openwrt/#%D0%B0%D0%B2%D1%82%D0%BE%D0%BC%D0%B0%D1%82%D0%B8%D1%87%D0%B5%D1%81%D0%BA%D0%B0%D1%8F-%D1%83%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0-%D0%B8-%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-%D1%87%D0%B5%D1%80%D0%B5%D0%B7-shell-%D1%81%D0%BA%D1%80%D0%B8%D0%BF%D1%82). Вставляем в терминал:
```bash
sh <(wget -O - https://raw.githubusercontent.com/itdoginfo/domain-routing-openwrt/master/getdomains-install.sh)
```

Должны увидеть следующее:

<center>{{< image src="/images/script1.png" >}}</center>

Выбираем Sing-box (если пользуемся VLESS/SS/Trojan), прописывая "3" в консоли. Затем выбираем утилиту для обхода подмены DNS провайдером. На AX3000T ставим Stubby (т.к. он более легковесный), также прописывая "3". И, наконец, выбираем свой регион. Таким образом установятся файлы с прописанными доменами залоченных в РФ и для пользователей из РФ сайтов.

{{< admonition warning >}}
Если после всех последующих процедур интернет так и не заработает, попробуйте заново установить скрипт, заранее откатившись на дефолтную OpenWRT ("System" -> "Reset to defaults"
 -> "Perform reset" в панели управления OpenWRT). На шаге выбора утилиты для обхода подмены DNS выберите "1" (No)
{{< /admonition >}}

По окончании процесса нас дисконектит, поскольку роутер ушёл в перезагрузку. 

<center>{{< image src="/images/script2.png" >}}</center>

## Подключение VPN
Переходим в нашу панель 3x-ui. В разделе "Подключения" создаём новое подключение / добавляем пользователя в уже существующее. Копируем обычную ссылку (не подписку, хотя может и она будет работать) и вставляем её в Nekobox `Ctrl + V`.

<center>{{< image src="/images/3x-ui.png" >}}</center>

{{< admonition info >}}
Нет Nekobox? Можно скачать с [github разработчика](https://github.com/MatsuriDayo/nekoray/releases) (будет называться "nekoray"). Выбираем последнюю версию для Windows в Assets -> разархивируем в любую папку -> активируем nekobox.exe. 
{{< /admonition >}}

<center>{{< image src="/images/nekobox1.png" >}}</center>

После того, как вставили ссылку, появилось подключение. ПКМ -> "Поделиться" -> "Экспортировать конфиг sing-box".

<center>{{< image src="/images/nekobox2.png" >}}</center>

Переходим в любой удобный текстовый редактор и вставляем скопированный конфиг. Нам нужны только "outbounds", поэтому остальное удаляем. Также можно удалить и "tags" из самих outbounds. Должно получиться что-то такое: 

<center>{{< image src="/images/config1.png" >}}</center>

Обратно в терминал. Тут открываем конфиг нашего sing-box: `nano /etc/sing-box/config.json` и удаляем имеющиеся outbounds. На их место вставляем наши сохранённые. Выглядеть должно это так (обратите внимание на наличие запятой после "]"):

{{< admonition warning >}}
Если открытый файл оказался пустым, проверьте, точно ли по этому пути лежит конфиг. Узнать можно через команду `nano /etc/config/sing-box` — в строке "option conffile" будет указан путь до нужного нам файла
{{< /admonition >}}

<center>{{< image src="/images/config2.png" >}}</center>

После сохранения изменений (`Ctrl + O` -> `Enter` -> `Ctrl + X`) можем перезапустить сервис командой `service sing-box restart`. Если в ответе видим "running" — уже хорошо. 

<u>**ГОТОВО!**</u> Вам открыт весь (ну почти) интернет! Проверьте, получается ли зайти в [chatgpt.com](https://chatgpt.com) и грузятся ли видео в [youtube.com](https://youtube.com). Если да, то поздравляю 🥳

## Свои правила маршрутизации
Вот скачали мы список, не факт ведь, что там перечислены все сайты, к которым нет доступа? Далеко не факт. Поэтому можно добавить и свои домены, которые мы хотели бы посещать через прокси.

Пишем `nano /etc/config/dhcp`. Перемещаемся в самый низ и пишем следующую строку: `config ipset`. На следующей строке вы пишете `<Tab>list name 'vpn_domains'`, а в последующих, начиная с клавиши `Tab`, должны стоять текста типа `list domain 'nelzagram.com'`, где вместо 'nelzagram.com' вы вставляете нужный домен. Примерно так это будет выглядеть:

<center>{{< image src="/images/config3.png" >}}</center>

Аналогично сохраняем и выходим, после перезапускаем firewall и dnsmasq: `service firewall restart && service dnsmasq restart`. Теперь, если зайдём на 2ip.ru, мы увидим, что он определяет нас как Эстонцев 😎

## Восстановление роутера
К сожалению, так случается, что роутер становится нерабочим после неудачной попытки перепрошивки. Но, к счастью, в ситуации с Xiaomi (по-крайней мере с AX3000T точно) данную проблему можно решить.

1. Первым делом качаем MIWIFIRpairTool. Найти ссылку можно [здесь](https://4pda.to/forum/index.php?showtopic=1074874&st=360#entry126588646) (скорее всего придётся зарегистрироваться на форуме). Само скачивание совершаем так: ПКМ на первую со скрина <u>**MIWIFIRepairTool**</u> -> копировать адрес ссылки -> вставляем ссылку в Edge. Браузеры типа Chrome будут блокировать скачивание из-за нестандартности ПО внутри архива.

<center>{{< image src="/images/recovery_4pda.png" >}}</center>

2. Подключаем патч-корд в WAN-порт (ближайший порт к питанию) роутера одним концом, и к пк другим. Качаем родную версию прошивки [тут](https://openwrt.org/inbox/toh/xiaomi/ax3000t#api_rce_support_status) в столбце "Stock Firmware URL", либо 1.0.49, если родная 1.0.31. Отключаем WI-FI на пк, затем заходим в настройки и редактируем сетевой интерфейс: для Windows 11 это Сеть и Интернет -> Ethernet -> Назначение IP -> Редактировать.

    <center>{{< image src="/images/recovery_settings1.png" >}}</center>

    Включаем IPv4 и вбиваем IP: `192.168.31.100` и маску: `255.255.255.0` -> Сохранить.

    <center>{{< image src="/images/recovery_settings2.png" >}}</center>

3. Открываем скаченную программу.. хм, как-то много "вопросиков".. но это нормально, просто китайские символы не распознаются. Нажимаем на "вопросики" в первой строке и выбираем скаченную прошивку.

    {{< admonition danger >}}
  Путь до файла должен быть указан исключительно на латинице, то есть имя пользователя должно быть на английском. Если это не так, перекиньте прошивку в корневую папку диска C:
    {{< /admonition >}}

    <center>{{< image src="/images/recovery_miwifi1.png" >}}</center>

    Далее жмём на правую нижнюю кнопку, в выпадающем меню выбираем Ethernet -> 192.168.31.100.

    <center>{{< image src="/images/recovery_miwifi2.png" >}}</center>

    Снова самая правая кнопка снизу.  

    А вот сейчас взаимодействие с роутером: отсоединяем кабель питания; зажимаем кнопку reset; не отпуская кнопку вставляем кабель питания обратно и ждём пока быстро не замигает оранжевый светодиод (опять же, кнопка reset зажата).

    <center>{{< image src="/images/router_last.png" >}}</center>

4. Роутер заморгал, значит кнопку можно отпустить. MIWIFIRepairTool покажет процесс загрузки, после окончания которого программу можно закрывать. Ждём, пока роутер быстро не замигает синим цветом.

    Снова отключаем и включаем питание. После этого диод должен медленно замигать оранжевым.

Вуаля! Можно начинать прошивку сначала :)

## The end
Вот гайд и подошёл к концу. Надеюсь, я смог вам помочь 🙂. Всем до скорого, и спасибо, что читаете!