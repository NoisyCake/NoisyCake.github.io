---
title: "Анонимность в сети"
date: 2024-11-13 16:50:57
type: posts
tags:
  - anonymity
draft: true
---

Дисклеймер: Материл носит исключительно информационный характер. Автор не призывает к каким-либо действиям.

В этой статье разберём:
* Как анонимно сёрфить интернет
* Как общаться безлично
* Как поставить безопасную ОС на свой пк
* Как оплачивать товары/услуги, не оставляя о себе информации
* Человеческие ошибки, деанонимизирующие пользователя
<!--more-->

**Примечание.** Рабочие proxy и VPN постоянно меняются, да и достойных мне лень искать, поэтому ссылки на подобные сервисы оставлять не буду.

## Способы анонимизации

Пожалуй, стоит начать с факта, который каждый наверняка не раз слышали: абсолютной анонимности в современном интернете не существует. Тем не менее, при должном упорстве можно обеспечить себе достойный уровень скрытности, при котором вы найдёте силы договориться со своей паранойей)

В этом разделе обсудим простые способы подмены IP-адреса при выходе в интернет. Уверен, большинство посетителей и-так знают о Proxy/VPN/Tor, поэтому нетерпеливым предлагаю перейти сразу к этому разделу. Желающим поподробнее ознакомиться с каждым способом, а также совсем юным анонимусам лучше начать читать пряяямо отсюда 👇

### Proxy
**Proxy** — наиболее простая технология, позволяющая скрывать настоящий IP, MAC адреса и прочее. Работает как отдельный пк (сервер), с большой долей вероятности расположенный вне страны пользователя и отправляющий запросы на посещаемый источник от своего имени. То есть вместо того, чтобы напрямую соединить хост с целевым сервисом, прокси-сервер пропускает трафик через себя и устанавливает соединения, выдавая свой IP и скрывая информацию юзера.

Важными недостатками технологии являются отсутствие шифрования трафика при использовании бесплатных посредников и весьма вероятное ведение логов, то есть записей посещаемых сайтов, отпечатка браузера, деталей ОС и прочей информации. Ну а совсем недобросовестные провайдеры могут пытаться перехватить данные, которые пользователь вводит на посещаемых ресурсах.

Лично я использую этот вариант только для посещения локально-заблокированных ресурсов, но не для подмена личности. И вам советую не ограничиваться лишь одним прокси, если нужно скрыться от посторонних глаз.

### VPN
**VPN** (Virtual Private Network) — более интересная технология по сравнению с предыдущей. В ней также используются удалённые сервера, но, в отличие от большинства прокси, пакеты дынных (то есть данные) проходят этап шифрования. Происходит это так: VPN-клиент на устройстве пользователя шифрует отправляемые запросы, которые затем отправляются по заранее созданному туннелю на удалённый сервер. Там данные расшифровываются, и запрос идёт уже с IP сервера прямиком к нужному сайту. Затем ответ от этого сайта возвращается к VPN-серверу, где шифруется и передаётся пользователю по тому же туннелю, а VPN-клиент дешифрует данные. В итоге при проходе через туннель данные нельзя "прослушать", а если и получится, то придётся их расшифровать. Также, само собой, конечный сервис НЕ будет в курсе, что на самом деле запрос отправлялся нами.

На следующем рисунке представлена поверхностная схема отправки запроса через VPN

<center>{{< image src="/images/openvpn_scheme.jpg" >}}</center>

Минусы VPN:

* Важно отметить, что хоть данные идут через зашифрованный туннель, интернет-трафик всё равно проходит через вашего провайдера, а значит этот самый провайдер спокойно фиксирует факт использования как VPN, так и Proxy, и даже Tor'a, что, кстати, будет не сильно подозрительным с текущей политикой РКН :)

* **DNS** — система преобразования доменных имён в IP-адреса. Это преобразование, осуществляющееся DNS-серверами провайдеров, происходит, когда вы нажимаете "Enter" в поисковой строке браузера. То есть вместо [noisycake.ru](https://noisycake.ru) будет переход по следующему IP-адресу: 185.199.109.153. А нужно всё это для удобства человека, ведь кому понравится забивать набор октет вместо понятных буквенных символов? 

  Так вот существует следующее понятие: **DNS leak** или утечка DNS, при которой DNS-запросы идут в обход Proxy/VPN/Tor, из-за чего провайдер видит адреса посещаемых вами сайтов. Она может произойти по нескольким причинам: неправильная настройка или "плохой" VPN, использование сторонних расширений браузера и т.д. Рекомендую ознакомиться с политикой вашего VPN-провайдера, покопаться в настройках, погуглить и запретить ОС обращаться к другим DNS-серверам, кроме как VPN'овским.

* {{< admonition danger >}}
Могут ли всё-таки "утечь" ваши данные? Однозначно. Ведь на сервере они становятся открытыми, и большинство поставщиков VPN услуг предоставляют их рекламным сервисам (в лучшем случае), а по запросу правоохранительных органов выкатывают им всё имеющееся без серьёзных споров. Особо опасными, как и в случае с прокси, являются бесплатные сервера.
{{< /admonition >}}

* **Главная проблема VPN.** Представим ситуацию: вы — житель прекрасной страны КНР — подключаетесь к VPN-серверу со своего домашнего ПК, IP адрес сетевой карты которого — `103.102.180.1`. Сервер отправляет запросы на целевой ресурс, который видит уже другой IP — `217.161.27.2`, в то время как ваш провайдер знает только о факте подключения к этому IP, но не о посещаемых сайтах. Казалось бы, никто никогда не узнает, где и кто вы есть на самом деле. Пьянящее чувство свободы слова даёт вам возможность высказать всё, что вы думаете о нынешнем устое, и вы пишите оскорбительный комментарий в адрес правительства КНР в местной соцсети. 

  Спецслужбы, поперхнувшись утренним кофе, негодуют из-за столь неподобающей наглости. Они тут же запрашивают у соцсети ваш IP, получают `217.161.27.2` и пробивают его. Хм, Лондон... Но неужто эти культурные, вежливые любители чая могли так нецензурно выразиться, да ещё и на чистом китайском? Далее спецслужбы запрашивают список пользователей, которые подключались к `217.161.27.2` в определённые дату и время у местных провайдеров. Через одного такого провайдера вы и сидели, а как мы помним, он сохраняет и ваш IP (`103.102.180.1`), и IP целевого ресурса, коим выступает VPN-сервер, и дату-время обращения к этому ресурсу. И-так, упомянутые адреса сопоставлены. Нужно лишь сделать ещё запрос по личности, скрывающейся за конечным IP. Ага, нашёлся! Теперь осталось самое главное — вежливо объяснить, почему ваш комментарий неуместен в данной ситуации. Короче говоря, ожидайте культурной дискуссии с людьми самой спокойной профессии.

  <center>{{< image src="/images/humster.jpg" height=450 width=600 >}}</center>

  В итоге вас раскрыли, даже не обращаясь к самому VPN-провайдеру. Спасёт ли вас цепочка из двух VPN? Скорее усложнит поиск, что тоже вроде неплохо, но дело в следующем..

  На множестве сайтов происходит сбор информации, именуемой cookie. Вы можете видеть это каждый день, соглашаясь принять передачу ваших данных. Cookie могут содержать как просто уникальный идентификатор пользователя, фамилию, имя или даже просто посещаемые странички сайта, так и идентификатор устройства. Допустим вы уже долго пользуетесь N-банком со своего пк без использования всяких Proxy и прочего. В один день вы решили, что нужно сёрфить интернет анонимно, прочитали пару статей и поняли, что вам нужна цепочка из двух VPN. Вы гуляете по просторам заблокированных ресурсов и вдруг захотели задонатить автору за его чудесные труды ;). С этой мыслью преходите на сайт N-банка и.. вот и всё. Cookie взяли своё, а точнее — IP адрес, и теперь в файлах хранится два адреса: вашего пк и VPN-сервера, которые сопоставляются и отправляются в облако и в случае чего будут переданы по запросу.

  {{< admonition info >}}
  Если хотите выяснить, какие конкретно данные собирает сайт, прочитайте их политику сбора данных. Конечно, о чём-то могут умолчать, но всё же знать хотя бы что-то лучше, чем ничего.
  {{< /admonition >}}

Я использую VPN также только для обхода блокировок, хотя это уже скорее оверинжениринг — обычного Proxy достаточно. А вот для анонимности одного VPN'а будет не хватать.

### Собственный VPN-сервер
Кратко обсудим тему собственного VPN'а. Данный способ посложнее предыдущих и требует базовых навыков программирования и администрирования.

Существуют такие штуки как VPS (Virtual Private Server) и VDS (Virtual Dedicated Server) — по сути синонимичные вещи, а именно виртуальные машины, расположенные на серверах. Вы же знаете, что такое [виртуальная машина](https://help.reg.ru/support/vydelennyye-servery-i-dc/vmware-virtualnyy-data-tsentr/sozdaniye-i-nastroyka-virtualnykh-mashin/chto-takoye-virtualnaya-mashina#0)? Собственно, когда вы покупаете VPS/VDS, вы получаете полноценный пк, которым можете управлять удалённо. 

Гайдов по поднятию своего VPN'а много и больше, да и ввиду отсутствия личного опыта содержательного контента не получится. Как будет желание, напишу статью и на эту тему, а пока поверхностная инструкция:

1. Покупаем VPS/VDS. Нам хватит 1 ядра и 1гб RAM. Разумеется, при покупке нужно выбирать расположение сервиса вне России. Скорее всего VPS выйдет дешевле, берите его — для нашей цели его более чем достаточно, а переплачивать за VDS смысла не имеет. 

2. Устанавливаем на купленном сервере любой тип VPN. Довольно объёмный шаг, который придётся очень долго расписывать. Если есть желание — можете разобраться, погуглив пару десятков страничек, а если нет — ждите отдельной статьи :)

\- А какой профит-то от своего VPN? Лучше уж не париться и купить подписку на готовое решение.  
\- Когда вы подключаетесь к VPN-серверу через провайдера, параллельно с вами с этого же IP работают и другие люди (сотни, тысячи...), из-за чего сайты начинают относиться к адресу с недоверием. Поэтому главный плюс: отсутствие бесконечных капч, проверок и блокировок со стороны веб-сервисов. При работе через VPN-провайдера также существует вероятность, что ваш местный провайдер заблокирует целый пул IP адресов (то есть все адреса серверов), из-за чего вы больше не сможете пользоваться своей подпиской 😨. 

\- А минусы будут?  
\- Анонимности как таковой вы всё равно не получите. Риски примерно такие же, как и при использовании обычного VPN: утечки, мягкотелость VPS-хостинга, неправильная настройка, сопоставление и пр.

### Tor
Здесь начинаются настоящие инструменты для анонимности. Разберёмся в том, как работает это чудо и на что стоит обратить внимание. 

На данный момент Tor является самым мощным средством обеспечения приватности, его работу поддерживают тысячи людей по всей планете. Если раньше он был невыносимо медленным, то сейчас им можно пользоваться достаточно комфортно.

#### Как это работает
**Tor** (The Onion Router) / Луковая маршрутизация — сеть из [прокси-серверов](#proxy) (также называют узлами, реле или нодами), в роли которых обычно выступают пк добровольцев. Каждый пакет данных проходит как минимум следующий путь: user -> клиент Tor -> входная (guard) нода -> промежуточная (middle) нода -> выходная (exit) нода -> целевой ресурс. 

<center>{{< image src="/images/tor_net.jpg" >}}</center>

{{< admonition info >}}
Все активные узлы находятся в [открытом доступе] и каждый час публикуются в [консенсусе (consensus)](https://consensus-health.torproject.org/consensus-health.html) особыми **девятью** узлами, называемыми **управляющими серверами** (directory authorities). 
{{< /admonition >}}

При подключении к сети тор цепочка нод выбирается случайно, при этом меняется каждые 10 минут. Вдобавок перед отправкой пакеты шифруются столько раз, сколько узлов в цепочке, и у каждого узла есть свой ключ, дешифрующий только определённый уровень. Таким образом каждая нода будет снимать один слой шифрования и передавать следующей уже от своего имени (поэтому называется **луковой** маршрутизацией), вплоть до выходной, где данные становятся девственно чистыми. В итоге каждый узел знает адреса только двух соседних сегментов, благодаря чему выходной узел не обладает информацией об исходном пользователе. Ответ на запрос повторят ту же самую цепочку, но в обратном порядке: по ходу прохождения через узлы накладываются уровни шифрования, а дешифровка происходит в тор-клиенте на пк пользователя. 

#### Мосты
Существует закрытый список нод, которые называются **мостами**. Их также поддерживают добровольцы, вот только список мостов не публичный. Так зачем они нужны? Неужели обычных реле уже не хватает? А дело в том, что раз список нод публичный, то его могут увидеть все, в том числе Роскомнадзор, который и заставляет провайдеров РФ блокировать все возможные ноды, а также сайты, связанные с тором. Подобная ситуация происходит во многих странах, поэтому разработчики луковой сети придумали способ обхода данной проблемы: непубликуемые в общем доступе узлы, которые также управляются добровольцами. 

{{< admonition info >}}
Помните, мы говорили о девяти управляющих серверах? Есть и десятый, который занимается актуализацией списка рабочих мостов. Наш герой без плаща...
{{< /admonition >}}

Интересный факт: На сегодняшний день доля мостов составляет около 20% от общего числа узлов. Ознакомиться с сегодняшним количеством узлов и другими метриками можно на официальном сайте: https://metrics.torproject.org/

<center>{{< image src="/images/relays_stat.png" >}}</center>

#### Подключаемый транспорт
Мосты используются вместе с **подключаемым транспортом** (pluggable transport) — инструментами, использующимися для обхода цензуры и даже скрытия факта использования тора.

Сегодня в браузере Tor можно увидеть 4 вида подключаемого транспорта, а именно:
|Мост|Описание, функционал|
|--|--|
|obfs4|Делает Tor-трафик похожим на случайные данные, чтобы его нельзя было распознать с помощью DPI (глубокой проверки пакетов), часто используемой для обнаружения и блокировки Tor. Именно этот мост вы скорее всего получите при запросе|
|meek|Перенаправляет Tor-трафик через крупные облачные платформы, такие как Google, Microsoft или Amazon, что делает его похожим на обычный HTTPS-трафик|
|Snowflake|Постоянно меняющиеся прокси, управляемые волонтёрами и настроенными так, чтобы сделать вид, что пользователь совершает видеозвонок, а не использует Tor|
|WebTunnel|Маскирует подключение к Tor так, чтобы оно выглядело, будто пользователь заходит на веб-сайт по протоколу HTTPS|








Для подключению к сети тор используется Tor-браузер.







Опять же, существуют и минусы. По-порядку:

1. Ноды могут прослушиваться. В целом логично, ведь узлы предоставляют добровольцы, 























**Режим параноика.** Чтобы стать более анонимным, следует использовать: конфиденциальные браузеры по типу [Brave](https://brave.com/ru/), а лучше Tor (обсудим позже) и безопасные поисковые системы, к примеру [DuckDuckGo](https://duckduckgo.com/).