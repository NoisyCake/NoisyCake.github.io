# Анонимность в сети. Часть I


Дисклеймер: Материл носит исключительно информационный характер. Автор не призывает к каким-либо действиям.

В этой статье разберём способы анонимизации пользователя, то есть подмены IP-адреса и скрытия трафика при выходе в интернет. Также поговорим о доступе к &lt;здесь слово с корнем &#34;блок&#34;&gt; (мда.. скоро вместо VPN нужно будет говорить &#34;альтернативный способ передачи пакетов в сети) в РФ ресурсам. Уверен, большинство посетителей и-так знают о Proxy/VPN/Tor, но если хотите поподробнее ознакомиться с каждым из них, то добро пожаловать. В ином случае ждите второй части статьи, где мы поговорим о способах анонимного общения, неотслеживаемых платежах, а также узнаем, как установить лучшую (субъективно) ОС для анонимизации.

{{&lt; admonition warning &gt;}}
В процессе написания
{{&lt; /admonition &gt;}}
&lt;!--more--&gt;

**Примечание.** Рабочие proxy и VPN постоянно меняются, к тому же их работоспособность зависит от большого количества факторов, поэтому ссылки на подобные сервисы оставлять не буду.

## Способы анонимизации

Пожалуй, стоит начать с факта, который каждый наверняка не раз слышал: абсолютной анонимности в современном интернете не существует. Тем не менее, достаточно легко можно обеспечить себе достойный уровень скрытности, при котором вы сможете договориться со своей паранойей)

Начнём с прокси и ВПН. Скорее как способы обхода &lt;слово с корнем &#34;блок&#34;&gt;, чем анонимизации, но понять базовый принцип работы будет полезно. 

### Proxy
**Proxy** — промежуточный сервер между пользователем и целевым ресурсом. Работает в качестве удалённого пк (ну это прям грубо сказано конечно...), отправляющий запросы на посещаемый веб-источник от своего имени. То есть вместо того, чтобы напрямую соединить **хост** (вас) с целевым сервисом, прокси-сервер пропускает трафик через себя и устанавливает соединения, заменяя исходный IP на собственный.

Важно понимать, что по умолчанию прокси работает при условии, что используемая программа поддерживает эту функцию. Конечно, можно подключить некоторые утилиты, позволяющие прогонять все пакеты через сервер, но лучше не заморачиваться и использовать [VPN](/articles/anonymity/#vpn). Также некоторые прокси не поддерживают шифрования, что в контексте анонимности является существенным минусом. 

Не вдаваясь в подробности, перечислим основные типы прокси по протоколам:
- HTTP — используется для передачи запросов по протоколу HTTP, но не шифрует трафик. Работает только с веб-страницами. Неимоверно сильно устарели из-за повсеместного использования HTTPS.
- HTTPS — передаёт данные по шифрованному каналу, защищая трафик между клиентом и веб-сервером. Также работает только с веб-страницами.
- SOCKS — более универсальный прокси-протокол, который работает с любым типом трафика (в том числе для приложений, не поддерживающих HTTP/HTTPS), но сам по себе не шифрует данные. Легко детектируется цензорами.
- Shadowsocks — частично рабочий, особенно хорош при использовании через клиент [Outline](https://getoutline.org/ru/), но его трафик уникален, поэтому довольно просто детектируется. Недавно был отличным протоколом для обхода &lt;слово с корнем &#34;блок&#34;&gt;, но сегодня уже не рекомендуется его использовать.
- VLESS — вкупе с XTLS-Reality является наиболее актуальным решением на сегодняшний день, поскольку умеет маскировать трафик под другой &lt;u&gt;выбранный&lt;/u&gt; сайт. Пока РКН блокировать такое решение не научился.

В части [по настройке своего VPN](/articles/anonymity/#%d1%81%d0%be%d0%b1%d1%81%d1%82%d0%b2%d0%b5%d0%bd%d0%bd%d1%8b%d0%b9-vpn-%d1%81%d0%b5%d1%80%d0%b2%d0%b5%d1%80) мы разберём, как запустить собственный сервер с протоколом VLESS.

{{&lt; admonition warning &gt;}}
Обратите внимание на минусы VPN в следующем разделе. Большинство из них применимо и к прокси.
{{&lt; /admonition &gt;}}

Как уже говорилось, приемлемый уровень анонимности такой способ не даст, но лично я использую его для посещения &lt;слово с корнем &#34;блок&#34;&gt; ресурсов.

### VPN
**VPN** (Virtual Private Network) — более интересная технология по сравнению с предыдущей. В ней также используются удалённые сервера, но, в отличие от большинства прокси, **пакеты дынных** (блоки данных / раздробленные данные) проходят этап шифрования. И также в отличие от прокси, VPN-клиент на хосте позволяет перенаправить **весь** интернет-трафик через VPN-сервер. Происходит это так: VPN-клиент на устройстве пользователя шифрует запросы, которые затем отправляются по заранее созданному туннелю на удалённый сервер. Там данные расшифровываются, и запрос идёт уже с IP сервера прямиком к нужному сайту. Затем ответ от этого сайта возвращается к VPN-серверу, где шифруется и передаётся пользователю по тому же туннелю, а VPN-клиент дешифрует данные. В итоге при проходе через туннель данные нельзя &#34;прослушать&#34;, а если и получится, то придётся их расшифровать. Также, само собой, конечный сервис НЕ будет в курсе, что на самом деле запрос отправлялся хостом.

На следующем рисунке представлена поверхностная схема отправки запроса через VPN

&lt;center&gt;{{&lt; image src=&#34;/images/openvpn_scheme.jpg&#34; &gt;}}&lt;/center&gt;

Есть несколько протоколов, реализующих работу VPN. Рассмотрим наиболее популярные:
- PPTP (Point-to-Point Tunneling Protocol) — один из старейших протоколов. Является быстро-работающим и легко-настраиваемым, но не рекомендуется к использованию из-за наличия серьёзных уязвимостей.
- L2TP/IPSec (Layer 2 Tunneling Protocol / Internet Protocol Security) — связка протоколов. Первый обеспечивает туннелирование, то есть группирование данных в пакеты для их конфиденциальной передачи через общедоступные сети, а второй — шифрование этих пакетов. Является безопасным, но считается устаревшим, хоть и встроен во все современные ОС.
- OpenVPN — современный и гибкий протокол (хоть и медленный). Шифрование происходит с помощью криптографических алгоритмов. Является одним из наиболее популярных решений для обхода цензуры.
- IKEv2/IPSec (Internet Key Exchange version 2 / IPSec) — быстрый (по сравнению с OpenVPN, L2TP) и стабильный протокол.
- WireGuard — относительно новый протокол с открытым исходным кодом. На данный момент чуть ли не самый популярный для обхода &lt;слово с корнем &#34;блок&#34;&gt;. Имеет &#34;апгрейд&#34; в виде [AmneziaWG](https://docs.amnezia.org/ru/documentation/amnezia-wg/).

Все они обеспечивают одну функцию, но различаются протоколами шифрования, скоростью передачи данных, сложностью настройки и поддерживаемыми платформами. 

{{&lt; admonition warning &gt;}}
Главная общая черта перечисленных VPN и Proxy-протоколов — РКН научился их всех детектировать и блокировать 😀 (кроме амнезии и VLESS) 
{{&lt; /admonition &gt;}}

Минусы VPN:

* Важно отметить, что хоть данные идут через зашифрованный туннель, интернет-трафик всё равно проходит через вашего провайдера, а значит этот самый провайдер спокойно фиксирует факт использования как VPN, так и Proxy, и даже Tor&#39;a, что, кстати, будет не сильно подозрительным с текущей политикой РКН :)

* **DNS** — система преобразования доменных имён в IP-адреса. Это преобразование, осуществляющееся DNS-серверами провайдеров, происходит, когда вы нажимаете &#34;Enter&#34; в поисковой строке браузера. То есть вместо [noisycake.ru](https://noisycake.ru) будет переход по следующему IP-адресу: 185.199.109.153. А нужно всё это для удобства человека, ведь кому понравится забивать набор октет вместо понятных буквенных символов? 

  Так вот существует следующее понятие: **DNS leak** или утечка DNS, при которой DNS-запросы идут в обход Proxy/VPN/Tor, из-за чего провайдер видит адреса посещаемых вами сайтов. Она может произойти по нескольким причинам: неправильная настройка или &#34;плохой&#34; VPN, использование сторонних расширений браузера и т.д. Рекомендую ознакомиться с политикой вашего VPN-провайдера, покопаться в настройках, погуглить и запретить ОС обращаться к другим DNS-серверам, кроме как VPN&#39;овским.

* {{&lt; admonition danger &gt;}}
Могут ли всё-таки &#34;утечь&#34; ваши данные? Однозначно. Ведь на сервере они становятся открытыми, и большинство поставщиков VPN услуг предоставляют их рекламным сервисам (в лучшем случае), а по запросу правоохранительных органов выкатывают им всё имеющееся без серьёзных споров. Особо опасными, как и в случае с прокси, являются бесплатные сервера.
{{&lt; /admonition &gt;}}

* **Главная проблема VPN.** Представим ситуацию: вы — житель прекрасной страны КНР — подключаетесь к VPN-серверу со своего домашнего ПК, IP адрес сетевой карты которого — `103.102.180.1`. Сервер отправляет запросы на целевой ресурс, который видит уже другой IP — `217.161.27.2`, в то время как ваш провайдер знает только о факте подключения к этому IP, но не о посещаемых сайтах. Казалось бы, никто никогда не узнает, где и кто вы есть на самом деле. Пьянящее чувство свободы слова даёт вам возможность высказать всё, что вы думаете о нынешнем устое, и вы пишите оскорбительный комментарий в адрес правительства КНР в местной соцсети. 

  Спецслужбы, поперхнувшись утренним кофе, негодуют из-за столь неподобающей наглости. Они тут же запрашивают у соцсети ваш IP, получают `217.161.27.2` и пробивают его. Хм, Лондон... Но неужто эти культурные, вежливые любители чая могли так нецензурно выразиться, да ещё и на чистом китайском? Далее спецслужбы запрашивают список пользователей, которые подключались к `217.161.27.2` в определённые дату и время у местных провайдеров. Через одного такого провайдера вы и сидели, а как мы помним, он сохраняет и ваш IP (`103.102.180.1`), и IP целевого ресурса, коим выступает VPN-сервер, и дату-время обращения к этому ресурсу. И-так, упомянутые адреса сопоставлены. Нужно лишь сделать ещё запрос по личности, скрывающейся за конечным IP. Ага, нашёлся! Теперь осталось самое главное — вежливо объяснить, почему ваш комментарий неуместен в данной ситуации. Короче говоря, ожидайте культурной дискуссии с людьми самой спокойной профессии.

  &lt;center&gt;{{&lt; image src=&#34;/images/humster.jpg&#34; height=450 width=600 &gt;}}&lt;/center&gt;

  В итоге вас раскрыли, даже не обращаясь к самому VPN-провайдеру. Спасёт ли вас цепочка из двух VPN? Скорее усложнит поиск, что тоже вроде неплохо, но дело в следующем..

  На множестве сайтов происходит сбор информации, именуемой cookie. Вы можете видеть это каждый день, соглашаясь принять передачу данных. Cookie могут содержать как просто уникальный идентификатор пользователя, фамилию, имя или даже просто посещаемые странички сайта, так и идентификатор устройства. Допустим вы уже долго пользуетесь N-банком со своего пк без использования всяких Proxy и прочего. В один день вы решили, что нужно сёрфить интернет анонимно, прочитали пару статей и поняли, что вам нужна цепочка из двух VPN. Вы гуляете по просторам заблокированных ресурсов и вдруг захотели задонатить автору за его чудесные труды ;). С этой мыслью преходите на сайт N-банка и.. вот и всё. Cookie взяли своё, а точнее — IP адрес, и теперь в файлах хранится два адреса: вашего пк и VPN-сервера, которые сопоставляются и отправляются в облако и в случае чего будут переданы по запросу.

  {{&lt; admonition info &gt;}}
  Если хотите выяснить, какие конкретно данные собирает сайт, прочитайте их политику сбора данных. Конечно, о чём-то могут умолчать, но всё же знать хотя бы что-то лучше, чем ничего.
  {{&lt; /admonition &gt;}}

Я использую VPN также только для обхода &lt;слово с корнем &#34;блок&#34;&gt;, хотя это уже скорее оверинжениринг — обычного Proxy достаточно. А вот для анонимности, как мы уже выяснили, одного VPN&#39;а будет не хватать.

### Собственный VPN-сервер
Кратко обсудим тему собственного VPN&#39;а. Данный способ посложнее предыдущих и требует базовых навыков администрирования.

Существуют такие штуки как VPS (Virtual Private Server) и VDS (Virtual Dedicated Server) — по сути синонимичные вещи, а именно виртуальные машины, расположенные на серверах. Вы же знаете, что такое [виртуальная машина](https://help.reg.ru/support/vydelennyye-servery-i-dc/vmware-virtualnyy-data-tsentr/sozdaniye-i-nastroyka-virtualnykh-mashin/chto-takoye-virtualnaya-mashina#0)? Собственно, когда вы покупаете VPS/VDS, вы получаете полноценный пк, которым можете управлять удалённо. 

Сейчас будет простенький гайд по установке своего VPN-сервера (вообще-то прокси-сервера, но уж так повелось их называть), так что примите боевую сидячую позицию. Использоваться будут 3X-UI, VLESS/XTLS-Reality. Если сейчас не поняли, объясню по ходу гайда.

1. Покупаем VPS/VDS. Порекомендовать какой-либо сервис не могу, разве что скажу, что сам пользуюсь [HSHP](https://hshp.host/virtual-servers), но он может затянуть с выдачей IP. Разумеется, при покупке нужно выбирать расположение сервера вне России. Нам хватит 1 ядра и 1гб RAM, желательно хотя бы 10гб на диске, ОС — Ubuntu 22.04 (для других дальнейшие инструкции могут не подойти).

&lt;center&gt;{{&lt; image src=&#34;/images/VPS.png&#34; &gt;}}&lt;/center&gt;

2. На своём пк открываем терминал/Bash/PowerShell от имени администратора. Пишем следующую команду `ssh root@ip_сервера`, где вместо &#34;ip_сервера&#34;, соответственно, вставляем ip своего сервера. Терминал предложит ввести пароль, скорее всего при покупке сервис VPS самостоятельно сгенерировал его, ищем на почте или на странице купленной услуги.

{{&lt; admonition info &gt;}}
SSH — сетевой протокол, позволяющий управлять удалённой ОС
{{&lt; /admonition &gt;}}

&lt;center&gt;{{&lt; image src=&#34;/images/ssh.png&#34; &gt;}}&lt;/center&gt;

Если вы видите приглашение типа `root@host_name` — вы внутри, поздравляю! Теперь сменим сгенерированный root пароль на свой. Пишем команду `passwd`, вставляем новый пароль и подтверждаем его. 

{{&lt; admonition info &gt;}}
Вставка текста в терминал осуществляется либо клавишами `Ctrl &#43; Shift &#43; V`, либо правой кнопкой мыши (PowerShell)
{{&lt; /admonition &gt;}}

root — стандартное имя учётной записи, поэтому оно первоочерёдно попадает под брутфорс-атаки (перебор пароля). Обезопасим себя, создав нового пользователя и запретив вход для root (также называют суперпользователем). Пишем `adduser новое_имя`, придумываем сложный пароль и вбиваем/вставляем его. Для генерации сложных паролей я использую KeePass. Дальше жмём `Enter` вплоть до вопроса. Жмём `y`. Добавляем нового пользователя с правами администратора: `usermod -aG sudo имя_пользователя`, выходим из системы командой `exit`, либо закрывая терминал.

&lt;center&gt;{{&lt; image src=&#34;/images/passwd.png&#34; &gt;}}&lt;/center&gt;

Зайдём под именем созданного пользователя: `ssh имя_пользователя@ip_сервера`, и проверим работоспособность командой `sudo ls -la /root`. Вывод есть? Значит всё хорошо, идём дальше.

&lt;center&gt;{{&lt; image src=&#34;/images/new_user.png&#34; &gt;}}&lt;/center&gt;

Так, пора бы обновить систему. Пишем `sudo apt update &amp;&amp; sudo apt full-upgrade -y` и ждём. 

Установим nano: `sudo apt install nano`, откроем конфиг SSH командой `sudo nano /etc/ssh/sshd_config` и изменим параметр &#34;PermitRootLogin&#34; с &#34;yes&#34; на &#34;no&#34;.

{{&lt; admonition info &gt;}}
nano — консольный текстовый редактор Linux (UNIX-подобных ОС). Он покажется довольно специфичным юзеру, пользовавшемся только графическими редакторами, поэтому вот пару советов, которые могут вам помочь:
1. Передвигать курсор мышкой не получится, впрочем как и во всём терминале, так что используйте для этого стрелки на клавиатуре.
2. Нажмите `Ctrl &#43; O -&gt; Enter`, чтобы сохранить файл; Нажмите `Ctrl &#43; X`, чтобы выйти из редактора.
{{&lt; /admonition &gt;}}

&lt;center&gt;{{&lt; image src=&#34;/images/nano.png&#34; &gt;}}&lt;/center&gt;

Так лучше, теперь root не сможет зайти в систему никоим образом. Ещё один важный шаг: нужно перевесить порт SSH с дефолтного на повыше. Открываем (если уже закрыли) тот же самый файл sshd_config, раскомментируем строку &#34;Port&#34;, убрав `#` и пишем произвольный номер порта. Нужно запомнить его.

{{&lt; admonition info &gt;}}
IP — адрес сетевого устройства, порт — адрес сетевой службы/программы на этом IP
{{&lt; /admonition &gt;}}

&lt;center&gt;{{&lt; image src=&#34;/images/nano2.png&#34; &gt;}}&lt;/center&gt;

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

&lt;center&gt;{{&lt; image src=&#34;/images/3x-ui.png&#34; &gt;}}&lt;/center&gt;



\- А какой профит-то от своего VPN? Лучше уж не париться и купить подписку на готовое решение.  
\- Когда вы подключаетесь к VPN-серверу через провайдера, параллельно с вами с этого же IP работают и другие люди (сотни, тысячи...), из-за чего сайты начинают относиться к адресу с недоверием. Поэтому главный плюс: отсутствие бесконечных капч, проверок и блокировок со стороны веб-сервисов. При работе через VPN-провайдера также существует вероятность, что ваш местный провайдер заблокирует IP серверов из-за подозрительной активности, а значит вы потеряете деньги 😨.

\- А минусы будут?  
\- Анонимности как таковой вы всё равно не получите. Риски примерно такие же, как и при использовании обычного VPN: утечки, мягкотелость VPS-хостинга, неправильная настройка, сопоставление и прочие неприятности.

### Tor
Здесь начинаются настоящие инструменты для анонимности. Разберёмся в том, как работает это чудо и на что стоит обратить внимание. 

На данный момент Tor является самым мощным средством обеспечения приватности, его работу поддерживают тысячи людей по всей планете. Если раньше он был невыносимо медленным, то сейчас им можно пользоваться достаточно комфортно.

#### Как это работает
**Tor** (The Onion Router) / Луковая маршрутизация — сеть из [прокси-серверов](#proxy) (также называют узлами, реле или нодами), в роли которых обычно выступают пк добровольцев. Каждый пакет данных проходит как минимум следующий путь: user -&gt; клиент Tor -&gt; входная (guard) нода -&gt; промежуточная (middle) нода -&gt; выходная (exit) нода -&gt; целевой ресурс. 

&lt;center&gt;{{&lt; image src=&#34;/images/tor_net.jpg&#34; &gt;}}&lt;/center&gt;

{{&lt; admonition info &gt;}}
Все активные входные узлы находятся в открытом доступе и каждый час публикуются в [консенсусе (consensus)](https://consensus-health.torproject.org/consensus-health.html) особыми **девятью** узлами, называемыми **управляющими серверами** (Directory Authorities). 
{{&lt; /admonition &gt;}}

При подключении к сети тор цепочка нод выбирается случайно, при этом меняется каждые 10 минут. Вдобавок перед отправкой пакеты шифруются столько раз, сколько узлов в цепочке, и у каждого узла есть свой ключ, дешифрующий только определённый уровень. Таким образом каждая нода будет снимать один слой шифрования и передавать следующей уже от своего имени (поэтому называется **луковой** маршрутизацией), вплоть до выходной, где данные становятся девственно чистыми. В итоге каждый узел знает адреса только двух соседних сегментов, благодаря чему выходной узел не обладает информацией об исходном пользователе. Ответ на запрос повторят ту же самую цепочку, но в обратном порядке: по ходу прохождения через узлы накладываются уровни шифрования, а дешифровка происходит в тор-клиенте на пк пользователя. 

Почему же используется только три узла в цепочке? Неужели больше — не лучше? Пожалуй, нет. Больше узлов = больше нагрузки на сеть Tor &#43; медленная скорость передачи данных, поэтому такое количество точек в цепи нужно для баланса между анонимностью и производительностью.

#### Мосты
Существует закрытый список нод, которые называются **мостами**. Они используются в качестве входных узлов, их также поддерживают добровольцы, вот только список мостов не публичный. Так зачем они нужны? Неужели обычных реле уже не хватает? А дело в том, что раз список входных нод публичный, то его могут увидеть все, в том числе, к примеру, Роскомнадзор, который и заставляет российских провайдеров блокировать доступ к ним. Подобная ситуация происходит во многих странах, поэтому разработчики луковой сети придумали способ обхода данной проблемы: непубликуемые в общем доступе узлы. 

При использовании моста цепочка-путь данных от юзера до целевого ресурса немного меняется. Теперь она выглядит так: user -&gt; клиент Tor -&gt; мост (bridge) -&gt; промежуточная нода -&gt; выходная нода -&gt; целевой ресурс.

{{&lt; admonition info &gt;}}
Помните, мы говорили о девяти управляющих серверах? Есть и десятый, который занимается актуализацией списка рабочих мостов. Наш герой без плаща...
{{&lt; /admonition &gt;}}

Интересный факт: На сегодняшний день доля мостов составляет около 20% от общего числа узлов. Ознакомиться с сегодняшним количеством узлов и другими метриками можно на официальном сайте: https://metrics.torproject.org/

&lt;center&gt;{{&lt; image src=&#34;/images/relays_stat.png&#34; &gt;}}&lt;/center&gt;

#### Подключаемый транспорт
Мосты работают вместе с **подключаемым транспортом** (pluggable transport) — инструментами, использующимися для обхода цензуры и скрытия факта использования тора.

Сегодня в браузере Tor можно увидеть 4 вида подключаемого транспорта, а именно:
|Мост|Описание, функционал|
|--|--|
|obfs4|Делает Tor-трафик похожим на случайные данные, чтобы его нельзя было распознать с помощью DPI (глубокой анализ пакетов), часто используемой для обнаружения и блокировки Tor, VPN и Proxy. Именно этот мост вы скорее всего получите|
|meek|Перенаправляет Tor-трафик через крупные облачные платформы, такие как Google, Microsoft или Amazon, что делает его похожим на обычный HTTPS-трафик|
|Snowflake|Постоянно меняющиеся прокси, управляемые волонтёрами и настроенными так, чтобы сделать вид, что пользователь совершает видеозвонок, а не использует Tor|
|WebTunnel|Маскирует подключение к Tor так, чтобы оно выглядело, будто пользователь заходит на веб-сайт по протоколу HTTPS|

Например, если используется мост с транспортом obfs4, то трафик сначала маскируется obfs4, а затем передаётся через мост. 

#### .onion-сервисы
И-так, onion-сайты... а хотя нет. Начнём с другого. 

**HTTP** — протокол (установленные правила), по которому пк пользователя обменивается данными с интернет ресурсами. Удобная штука, правда есть его прокаченный собрат — **HTTPS** — расширенный HTTP протокол, устанавливающий зашифрованное соединение с помощью криптографических протоколов SSL и TLS, то есть при его использовании запросы и ответы шифруются, чтобы не было интереса их перехватывать. Узнать, безопасно ли ваше соединение, можно, посмотрев на адресную строку браузера: если вначале стоит `https://` — всё в порядке. В настоящее время HTTPS протокол используется на большинстве сайтах, и при использовании Tor&#39;a нужно открывать ресурсы именно с ним (вернёмся к причине позже), о чём он предупреждает всякий раз, когда пользователь пытается открыть страницы с &#34;плохим&#34; протоколом HTTP.

**Onion-сайты** — веб-ресурсы, доступные только через луковую сеть и использующие доменную зону `.onion`. Они лучше обычных сайтов тем, что:

1) Используют все плюсы HTTPS протокола, а значит трафик защищается сквозным шифрованием.
2) Домены не хранятся на централизованных DNS-серверах, что затрудняет их обнаружение и даёт устойчивость к [DNS-атакам](https://ru.wikipedia.org/wiki/DNS_spoofing). Но где же тогда они расположены? На специальных узлах, работающих примерно как DNS-сервера, только для каждого ресурса он будет своим.
3) Владельцам onion-сайтов не требуется покупать доменные имена, так как они формируются автоматически в рамках протокола шифрования.
4) Работают на основе криптографических ключей. Публичный как раз используется для создания домена, а приватный — для шифрования и аутентификации.

{{&lt; admonition info &gt;}}
Onion-адрес состоит из 56 букв и цифр с окончанием `.onion`
{{&lt; /admonition &gt;}}

#### Как подключиться к сети Tor
Вот мы и подошли к практической части!

{{&lt; admonition info &gt;}}
Все ссылки предоставлены из надёжных источников. Вероятнее всего для доступа ко многим из них вам потребуется Proxy/VPN.
{{&lt; /admonition &gt;}}

Для подключения к сети тор используется Tor-браузер. Скачиваем его [отсюда](https://www.torproject.org/ru/download/). К сожалению, iOS не позволяет разработать Tor под себя, но разработчики рекомендуют скачать [Onion Browser](https://apps.apple.com/us/app/onion-browser/id519296448), который тесно сотрудничает с проектом Tor. 

Далее, если вы не не хотите использовать VPN, вам потребуются мосты (вспоминаем факт блокировки входных нод). Рассмотрим все известные способы их подключения:
* На пк можно прямо из настроек браузера в разделе &#34;подключение&#34; нажать кнопку &#34;Запросить мосты&#34; и.. получить их :)

&lt;center&gt;{{&lt; image src=&#34;/images/tor_bridges1.png&#34; &gt;}}&lt;/center&gt;

* Вряд-ли такой подход сработает, но можете попробовать выбрать один из предложенных вариантов во вкладке &#34;Выбрать встроенный мост&#34;.

* Или же можно запросить мосты прямиком из [официального бота](https://t.me/GetBridgesBot). Полученные адреса копируем, нажимаем на &#34;Добавить новые мосты&#34; и вставляем.

&lt;center&gt;{{&lt; image src=&#34;/images/tor_bridges2.png&#34; &gt;}}&lt;/center&gt;

* Если вышеуказанные способы не работают, попробуйте написать на почту bridges@torproject.org. Важно: используйте только Riseup или Gmail, также оставьте тему сообщения пустым, а в теле напишите `get transport obfs4`.

{{&lt; admonition info &gt;}}
Если всё-таки ничего из перечисленного вам не помогло, попробуйте включить VPN перед подключением к Tor, хотя сами разработчики луковой сети не рекомендуют это делать, о чём мы уже скоро поговорим.
{{&lt; /admonition &gt;}}

Ура, мы в сети! Но что дальше? Как найти то, за чем я сюда пришёл? Гуглить! Или онионить... Сейчас скорее всего в качестве поисковика у вас стоит DuckDuckGo или Startpage. Они действительно хороши в плане анонимности, правда первый понижает в результатах выдачи российские новости, связанные с СВО (как же наверное приятно, когда тебе не нужно включать голову и думать, где правда, а где — нет), поэтому я чаще использую второй, хотя он тоже может блокировать некоторые запросы, нарушающие моральные или законодательные нормы (мда, звучит абсурдно). В связи с чем советую поискать ссылки на такие поисковики как Torch, Ahmia, Tordex, Bobby и т.п. А ещё есть отдельные сайты, которые выкладывают список onion-ссылок по разным тематикам: соцсети, торговля, форумы и т.п. 

{{&lt; admonition info &gt;}}
Если определённый сайт не пускает вас из-за подозрительного трафика, попробуйте создать новую личность в браузере. Для этого нажмите на такой &lt;i class=&#34;fa-solid fa-bars&#34;&gt;&lt;/i&gt; значок в правом верхнем углу, затем на кнопку &#34;Новая личность&#34;. Построится новая цепочка сети Tor, закроются все вкладки, очистятся куки, данные о скачанных вами файлах и прочая информация.
{{&lt; /admonition &gt;}}

#### Проблемы Tor
Tor, как и любое другое средство, не гарантирует 100%-ой анонимности. Существуют уязвимости, которые могут деанонимизировать пользователя.

1. Узлы могут прослушиваться. В целом логично, ведь их предоставляют добровольцы, а значит этими добровольцами могут оказаться недоброжелатели :) Если скомпрометирована только одна нода в цепочке, то не произойдёт ничего страшного, так как guard node знает только отправителя, но не знает данных и конечного получателя, middle node не знает вообще ничего критически важного, а exit node знает конечного получателя и данные, но не отправителя.

    Беда наступает, когда одним злоумышленником контролируются входная и выходная ноды. Сопоставив время и объём передаваемых данных будет достаточно просто установить реальный IP, а если скомпрометированы все 3 ноды, то пиши-пропало... До появления guard nodes (вместо них раньше были **entry nodes**) и мостов эта проблема была особенно актуальной, так как при длительном использовании тора шанс быть раскрытым был практически стопроцентный, ведь по теории вероятности вы рано или поздно наткнётесь на цепочку, в которой обе ноды будут скомпрометированы. 
    
    Сейчас же, вместо того, чтобы менять всю цепочку &lt;u&gt;раз в 10 минут&lt;/u&gt;, меняются только средняя и выходная ноды. Guard ноды же устроены по-другому: для каждого Tor-клиента случайным образом подбираются несколько входных узлов, и при подключении используются только они, при этом меняются &lt;u&gt;раз в 2-3 месяца&lt;/u&gt;. Если эти узлы не контролируются, злоумышленник никогда не сможет деанонимизировать пользователя. В ином случае утекает значительная часть данных, но не больше, чем если бы такого трюка не было вовсе. С одной стороны это не очень гуманно, поскольку все проблемы достаются только отдельным пользователям, а с другой — большая часть людей сильнее защищена. Что ж, зачастую приходится идти на компромиссы. 
    
    Ну а мосты вообще можно не менять, пока не перестанут работать. Функционал у них практически такой же, как и у Guard нод, плюсом они усложняют анализ трафика и скрывают факт использования тора.

    Также помните, что стоит заходить только на веб-сервисы, которые используют HTTPS. Так выходной узел не сможет видеть в ответе от сайта ваши логины и пароли.

    Вообще подобная вещь как контроль большого количества нод невероятно ресурсозатратно, так что чересчур сильно беспокоиться об этом не стоит, тем более что лучших простых и бесплатных альтернатив (по сравнению с тором) пока нет.  

    {{&lt; admonition warning &gt;}}
Не отключайте NoScript. Это расширение защищает вас от JavaScript-атак. JavaScript довольно мощный инструмент, который может извлекать реальный IP в обход прокси-серверов, собирать данные о системе (разрешение экрана, шрифты, часовой пояс и прочее) и выполнять запросы на внешние ресурсы от имени пользователя.
    {{&lt; /admonition &gt;}}

2. Теоретически злоумышленниками могут оказаться и Directory Authorities, о которых мы не так давно говорили. В таком случае они могут включать в консенсус скомпрометированные ноды, но риск остаётся минимальным благодаря репутации данных узлов (да, вам в любом случае приходится кому-то доверять).

3. НЕЛЬЗЯ (ну прям не надо, серьёзно) загружать torrent-файлы через Tor. Если вкратце, то большинство клиентов Bittorrent (сетевой протокол для обмена torrent-файлами), такие как uTorrent, игнорируют настройки прокси-серверов и обращаются напрямую к пользователю, детектируя его реальный IP и случайный порт и отправляют эту информацию другим пользователям uTorrent&#39;a. Ах да, использование Bittorrent&#39;a создаёт большую нагрузку на сеть Tor, из-за чего у всех пользователей возникают падение скорости передачи данных. Так не будем же создавать сообществу проблемы ;)

4. На разных форумах много людей беспокоятся о том, что Tor финансируется различными структурами США, причём в довольно внушительном объёме (50% - 90%). Раньше конкретные суммы были публичными, но, видимо из-за наплыва трясущихся анонимусов, разработчики решили не развивать паранойю, и стали скрывать эту информацию. Оставлю свою точку зрения: отдельные госструктуры США финансируют Tor потому что сами им пользуются. Также стоит помнить, что исходный код Tor открыт и многократно проверялся криптоаналитиками и экспертами по кибербезопасности из таких компаний, как Rapid7, NCC Group, MIT, Stanford, Mozilla и др., поэтому опасения на счёт потенциального контроля считаю необоснованными.

### Связки нескольких способов
FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF

### Советы
Перейдём к незамысловатым советам, которые помогут вам оставаться анонимными, и подведём промежуточные итоги.

* Не устанавливайте дополнительные плагины и расширения в браузер Tor. Подобные дополнения дают уникальный &#34;отпечаток&#34; браузеру, такие как изменения заголовков запросов, действий на веб-сайтах, что может быть использовано для вашего отслеживания.

* Не открывайте скаченные файлы, пока находитесь в сети Tor. Они могут содержать скрипты, самооткрывающиеся ссылки и прочие фокусы, которых вы можете даже не заметить. Для подобных действий лучше использовать отдельную виртуальную машину, не имеющей доступа в интернет и не содержащей никакой критически важной для вас информации.

* Не заходите через тор в свои аккаунты из обычной жизни, не используйте и не распространяйте свои личные данные и не общайтесь со своими знакомыми через новые анонимные аккаунты.

* Шифруйте диски.

* Банальный, но важный совет, которым многие пренебрегают: всегда придумывайте разные пароли, а лучше генерируйте и храните их в проверенной оффлайн программе по типу KeePass. Также можете сохранить пару важных паролей (доступ к системе/дешифровка диска/доступ к базе данных паролей) прямо на бумажке, но нужно постараться спрятать её.

Дальше для полных параноиков 👽:

* Не покупайте физические сим-карты для одноразовых аккаунтов. Даже в переходах метро. Гораздо лучше покупать одноразовые номера в соответствующих сервисах. 

* На каждом форуме/аккаунте придумывайте себе новую личность. Меняйте свои интересы (либо не выдавайте их совсем) и стиль общения.

* Выключайте микрофоны и камеры. Сегодня даже ИИ может определить конкретную клавишу лишь по звуку её нажатия, что уж говорить об обученных специалистах.

* Убирайте мобильный телефон подальше. Конечно, просто так, без установки ПО или физических устройств на мобильное устройство, вас прослушать вряд-ли смогут, но раз зашла речь про паранойю.. 

* [Не заходите на Фейсбук](https://xakep.ru/2020/06/11/facebook-vs-brian-kil/) :)

* Не платите Bitcoin&#39;ом. В следующей части статьи мы обсудим этот момент подробнее.

* Не используйте Windows. Подробнее также в следующей части ;)

Как раз-таки Tor я использую как основное средство анонимизации. Лучших альтернатив просто не существует.

Информации много, поэтому решил разделить статью на две части. Во второй начнётся самое интересное. Жду вашего мнения в лс, и спасибо, что читаете!

---

> Автор: [NoisyCake](https://t.me/noisy_cake)  
> URL: http://localhost:1313/articles/vps_server/  
