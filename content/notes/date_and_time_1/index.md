---
title: "Работа с датой и временем. Часть I"
date: 2024-06-16 00:00:01
type: posts
tags:
  - python
  - datetime
  - date
  - modules
  - datatypes
categories:
  - python
collections:
  - Date&Time
draft: false
---

Модули и типы данных для работы со временем, датой и временными промежутками в языке программирования Python. Локализация
<!--more-->
## Типы данных `date` и `time`
Для удобной работы с датой и временем в Python есть модуль `datetime`. Он состоит из нескольких типов данных. Благодаря их наличию, программист получает доступ ко многим полезным функциям:
* получение текущих системных даты и времени;
* вычисление разницы между датами и другие арифметические операции над ними;
* сравнение даты и времени;
* форматированный вывод информации о дате и времени.

### Типы данных модуля `datetime`
|Название типа<br>данных|<center>Характеристика</center>|
|:--:|:--|
|`date`|Представляет собой информацию о дате на основе Григорианского календаря|
|`time`|Представляет собой информацию о времени|
|`datetime`|Содержит информацию о времени и дате на основе Григорианского календаря|
|`timedelta`|Описывает определённый период во времени, который находится между двумя различными моментами|
|`tzinfo`|Представляет различные сведения о часовом поясе|
|`timezone`|Содержит информацию о времени в формате UTC|

---
### Тип данных `date`
Для использования: `from datetime import date`.

Тип данных (класс) `date` используется для представления данных о дате и включает информацию о годе, месяце и дне.  
При создании новой даты нужно указать год, месяц и день. При этом, как и положено, нельзя указать значение `day` большее 31 и/или значение `month` большее 12:
```py
from datetime import date

my_date = date(1992, 10, 6)  # тип date: год + месяц + день

print(my_date)
print(type(my_date))
```

```
# Вывод:
1992-10-06
<class 'datetime.date'>
```

Получить доступ к отдельным составляющим даты можно с помощью атрибутов `year`, `month`, `day`:
```py
from datetime import date

my_date = date(1992, 10, 6)

print('Год =', my_date.year)
print('Месяц =', my_date.month)
print('День =', my_date.day)
```

```
# Вывод:
Год = 1992
Месяц = 10
День = 6
```

Метод `today()` нужен, чтобы получить текущую дату с компьютера: `today_date = date.today()`

С помощью метода `weekday()` можно определить день недели (нумерация начинается с нуля: 0=понедельник, 1=вторник, ..., 6=воскресенье).  
Если нужна нумерация с единицы, можно воспользоваться методом `isoweekday()`.

Атрибуты `min` и `max` используются, чтобы получить минимальную и максимальную соответственно возможную дату (в рамках типа данных `date`):
```py
from datetime import date

print(date.min)
print(date.max)
```

```
# Вывод:
0001-01-01
9999-12-31
```

Метод `fromordinal()` позволяет создать дату из номера дня, начиная с `0001-01-01`, а метод `toordinal()`, наоборот, преобразует дату в номер дня:
```py
from datetime import date

date1 = date.fromordinal(365)
date2 = date(1999, 12, 26)

print(date1)
print(date2.toordinal())
```

```
# Вывод:
0001-12-31
730114
```

---
### Тип данных `time`
Для использования: `from datetime import time`

Тип данных (класс) `time` используется для представления данных о времени и включает информацию о часах, минутах, секундах и микросекундах (одна миллионная доля секунды).  
При создании времени нужно указать часы, минуты, секунды и микросекунды. При этом, значение `hour` не может быть больше 23, а значение `second` больше 59:
```py
from datetime import time

my_time = time(11, 20, 54, 1234)  # тип time: часы + минуты + секунды + микросекунды

print(my_time)
print(type(my_time))
```

```
# Вывод:
11:20:54.001234
<class 'datetime.time'>
```

В отличие от дат (тип данных `date`), чтобы создать объект типа `time`, необязательно указывать все его атрибуты в конструкторе. Недостающие данные о времени автоматически заполняются нулями.

Так же, как и при работе с типом данных `date`, пользуясь типом `time`, можно получать доступ к отдельным значениям созданного времени: часам, минутам, секундам и микросекундам с помощью атрибутов `hour`, `minute`, `second`, `microsecond`:
```py
from datetime import time

my_time = time(11, 20, 54, 1234)

print('Часы =', my_time.hour)
print('Минуты =', my_time.minute)
print('Секунды =', my_time.second)
print('Микросекунды =', my_time.microsecond)
```

```
# Вывод:
Часы = 11
Минуты = 20
Секунды = 54
Микросекунды = 1234
```

{{< admonition danger >}}
В случае использования неверного значения для атрибутов (`year`, `month`, `day` и т.д.) возникает ошибка (исключение) `ValueError`.
{{< /admonition >}}

---
### Связь дат и времени
`date` и `time` можно сравнивать с помощью операторов `==`, `!=`, `<`, `>`, `<=` и `>=`:
```py
from datetime import date, time

date1 = date(2022, 10, 15)
date2 = date(1999, 12, 26)

time1 = time(13, 10, 5)
time2 = time(21, 32, 59)

print(date1 < date2)
print(time1 < time2)
```

```
# Вывод:
False
True
```

При работе с этими типами данных можно использовать встроенные функции `min()`, `max()` и `sorted()`:
```py
from datetime import date

dates = [date(2021, 12, 31), date(2025, 3, 19), date(2017, 5, 25)]

print(min(dates))
print(max(dates))
print(sorted(dates))
```

```
# Вывод:
2017-05-25
2025-03-19
[datetime.date(2017, 5, 25), datetime.date(2021, 12, 31), datetime.date(2025, 3, 19)]
```

{{< admonition info >}}
Оба типа данных `date` и `time` являются неизменяемыми
{{< /admonition >}}

---
### Функции `str()` и `repr()`
Встроенная функция `str()` возвращает объект в **неформальном (понятном человеку)** строковом представлении. В следующем коде `str()` вызывается неявно и преобразует указанные объекты в понятный нам вид:
```py
from datetime import date, time

my_date = date(2021, 12, 31)
my_time = time(11, 20, 54)

print(str(my_date))  # Аналогичен коду print(my_date)
print(str(my_time))  # Аналогичен коду print(my_time)
```

```
# Вывод:
2021-12-31
11:20:54
```

Встроенная функция `repr()` возвращает объект в **формальном (понятном интерпретатору)** строковом представлении:
```py
print(repr(my_date))
print(repr(my_time))
```

```
# Вывод:
datetime.date(2021, 12, 31)
datetime.time(11, 20, 54)
```

Для встроенных типов данных при печати одиночного значения объекта явно вызывать функцию `str()` не требуется, однако при печати списка таких объектов может произойти следующее:
```py
from datetime import date

dates = [date(2021, 12, 31), date(2019, 10, 6), date(2022, 11, 8)]  # список дат

print(dates)

# Вывод: [datetime.date(2021, 12, 31), datetime.date(2019, 10, 6), datetime.date(2022, 11, 8)]
```

Поэтому, если нам нужно вывести дату или время в нормальном виде, нужно воспользоваться распаковкой (`str()` также будет вызвана неявно):
```py
print(*dates, sep=', ')

# Вывод: 2021-12-31, 2019-10-06, 2022-11-08
```

---
### Форматирование даты и времени
По умолчанию вывод даты и времени осуществляется в ISO формате:
* дата имеет вид: `YYYY-MM-DD`
* время имеет вид: `HH:MM:SS` или `HH:MM:SS.ffffff`

Для форматированного вывода даты и времени используется метод `strftime()` (для обоих типов `date` и `time`):
```py
from datetime import date, time

my_date = date(2021, 8, 10)
my_time = time(7, 18, 34)

print(my_date)                           # вывод в ISO формате
print(my_time)                           # вывод в ISO формате

print(my_date.strftime('%d/%m/%y'))      # форматированный вывод даты
print(my_date.strftime('%A %d, %B %Y'))  # форматированный вывод даты
print(my_time.strftime('%H.%M.%S'))      # форматированный вывод времени
```

```
# Вывод:
2021-08-10
07:18:34
10/08/21
Tuesday 10, August 2021
07.18.34
```

Таблица аргументов параметра:
|Формат|Значение|Пример|
|--|--|--|
|`%a`|Сокращённое название дня недели|Sun, Mon, …, Sat (en_US)<br>Пн, Вт, ..., Вс (ru_RU)|
|`%A`|Полное название дня недели|Sunday, Monday, …, Saturday (en_US)<br>понедельник, ..., воскресенье (ru_RU)|
|`%w`|Номер дня недели [0, …, 6]|0, 1, …, 6 (0=воскресенье, 6=суббота)|
|`%d`|День месяца [01, …, 31]|01, 02, …, 31|
|`%b`|Сокращенное название месяца|Jan, Feb, …, Dec (en_US);<br>янв, ..., дек (ru_RU)|
|`%B`|Полное название месяца|January, February, …, December (en_US);<br>Январь, ..., Декабрь (ru_RU)|
|`%m`|Номер месяца [01, …,12]|01, 02, …, 12|
|`%y`|Год без века [00, …, 99]|00, 01, …, 99|
|`%Y`|Год с веком|0001, 0002, …, 2013, 2014, …, 9999<br>В Linux год выводится без ведущих нулей:<br>1, 2, …, 2013, 2014, …, 9999|
|`%H`|Час (24-часовой формат) [00, …, 23]|00, 01, …, 23|
|`%I`|Час (12-часовой формат) [01, …, 12]|01, 02, …, 12|
|`%p`|До полудня или после (при 12-часовом формате)|AM, PM (en_US)|
|`%M`|Число минут [00, …, 59]|00, 01, …, 59|
|`%S`|Число секунд [00, …, 59]|00, 01, …, 59|
|`%f`|Число микросекунд|000000, 000001, …, 999999|
|`%z`|Разница с UTC в формате ±HHMM[SS[.ffffff]]|+0000, -0400, +1030, +063415, ...|
|`%Z`|Временная зона|UTC, EST, CST|
|`%j`|День года [001,366]|001, 002, …, 366|
|`%U`|Номер недели в году (неделя начинается с воскр.).<br>Неделя, предшествующая первому воскресенью, является нулевой. [00, …, 53]|00, 01, …, 53|
|`%W`|Номер недели в году (неделя начинается с пон.).<br>Неделя, предшествующая первому понедельнику, является нулевой. [00, …, 53]|00, 01, …, 53|
|`%c`|Дата и время|Tue Aug 16 21:30:00 1988 (en_US);<br>03.01.2019 23:18:32 (ru_RU)|
|`%x`|Дата|08/16/88 (None); 08/16/1988 (en_US);<br>03.01.2019 (ru_RU)|
|`%X`|Время|21:30:00|

При форматировании даты все временные характеристики сбрасываются в минимально возможные (нулевые значения). Аналогично при форматировании времени все характеристики даты сбрасываются в минимально возможные:
```py
from datetime import date, time

my_date = date(2021, 8, 10)
my_time = time(7, 18, 34)

print(my_date.strftime('%a %A %w %d %b %B %m %y %Y %H %I %p %M %S %f %z %Z %j %U %W %c %x %X'))
print(my_time.strftime('%a %A %w %d %b %B %m %y %Y %H %I %p %M %S %f %z %Z %j %U %W %c %x %X'))
```

```
# Вывод:
Tue Tuesday 2 10 Aug August 08 21 2021 00 12 AM 00 00 000000   222 32 32 Tue Aug 10 00:00:00 2021 08/10/21 00:00:00
Mon Monday 1 01 Jan January 01 00 1900 07 07 AM 18 34 000000   001 00 01 Mon Jan  1 07:18:34 1900 01/01/00 07:18:34
```

* Для создания новой даты на основе уже существующей можно использовать метод `replace()`. Он возвращает новую дату с переданными изменёнными значениями атрибутов `year, month, day`:
```py
from datetime import date

date1 = date(1992, 10, 6)
date2 = date1.replace(year=1995)         # заменяем год           
date3 = date1.replace(month=12, day=17)  # заменяем месяц и число

print(date1)
print(date2)
print(date3)
```

```
# Вывод:
1992-10-06
1995-10-06
1992-12-17
```

{{< admonition info >}}
Аналогичным способом можно создать и новое время на основе существующего.
{{< /admonition >}}

* Для того, чтобы получить строковое представление объектов типа `date` и `time` в ISO формате, можно воспользоваться методом `isoformat()`:
```py
from datetime import date, time

my_date = date(2021, 12, 31)
my_time = time(21, 15, 17)

print('Дата: ' + my_date.isoformat())   # Аналогично коду print('Дата: ' + str(my_date))
print('Время: ' + my_time.isoformat())  # Аналогично коду print('Дата: ' + str(my_time))
```

```
# Вывод:
Дата: 2021-12-31
Время: 21:15:17
```

* А чтобы преобразовать строку из ISO формата в объект `date` или `time`, можно воспользоваться методом `fromisoformat()`.

---
### Использование локализации
Для использования: `import locale`

Приведённый ниже код устанавливает русскую локализацию:
```py
from datetime import date
import locale

locale.setlocale(locale.LC_ALL, 'ru_RU.UTF-8')

my_date = date(2021, 8, 10)
print(my_date.strftime("%A %d, %B %Y"))  # форматированный вывод даты в русской локализации

# Вывод: вторник 10, Август 2021
```

---
## Тип данных `datetime`
Для использования: `from datetime import datetime`

Этот тип данных позволяет работать одновременно и с датой, и с временем. Он является неизменяемым. При создании новой даты-времени (тип `datetime`) нужно указать год, месяц, день, часы, минуты, секунды и микросекунды. При этом год, месяц и день являются <u>обязательными</u>, а часы, минуты, секунды и микросекунды <u>необязательными</u>:
```py
from datetime import datetime

my_datetime = datetime(1992, 10, 6, 9, 40, 23, 51204)  # создаем полную дату-время
only_date = datetime(2021, 12, 31)                     # создаем дату-время с нулевой временной информацией

print(my_datetime)
print(only_date)
print(type(my_datetime))
```

```
# Вывод:
1992-10-06 09:40:23.051204
2021-12-31 00:00:00
<class 'datetime.datetime'>
```

Так же, как и при работе с типами `date` и `time`, в `datetime` с помощью атрибутов (`year`, `month`, `day`, `hour`, `minute`, `second` и `microsecond`) можно получать доступ к отдельным значениям созданной даты-времени: годам, месяцам, дням, часам, минутам, секундам и микросекундам:
```py
from datetime import datetime

my_datetime = datetime(1992, 10, 6, 9, 40, 23, 51204)

print('Год =', my_datetime.year)
print('Месяц =', my_datetime.month)
print('День =', my_datetime.day)
print('Часы =', my_datetime.hour)
print('Минуты =', my_datetime.minute)
print('Секунды =', my_datetime.second)
print('Микросекунды =', my_datetime.microsecond)
```

```
# Вывод:
Год = 1992
Месяц = 10
День = 6
Часы = 9
Минуты = 40
Секунды = 23
Микросекунды = 51204
```

{{< admonition info >}}
Тип данных `datetime` наследует весь функционал (атрибуты и методы) от типа `date`
{{< /admonition >}}

---
### Методы `datetime`
* Сформировать новый объект типа `datetime` можно с помощью двух разных объектов, представляющих дату и время (`date` и `time`). Для этого используется метод `combine()`:
```py
from datetime import date, time, datetime

my_date = date(1992, 10, 6)
my_time = time(10, 45, 17)
my_datetime = datetime.combine(my_date, my_time)

print(my_datetime)

# Вывод: 1992-10-06 10:45:17
```

* Если же, наоборот, нужно из `datetime` получить `date` и `time`, то используются методы `date()` и `time()` соответственно:
```py
from datetime import datetime

my_datetime = datetime(2022, 10, 7, 14, 15, 45)
my_date = my_datetime.date()  # получаем только дату (тип date)
my_time = my_datetime.time()  # получаем только время (тип time)

print(my_datetime, type(my_datetime))
print(my_date, type(my_date))
print(my_time, type(my_time))
```

* Метод `now()` используется для получения локального времени:
```py
from datetime import datetime

print(datetime.now())
print(datetime.utcnow())
```

При этом, если нужно отобразить код определённого часового пояса, рекомендуется использовать код:
```py
import datetime as dt

print(dt.datetime.now(dt.timezone.utc))
```

* Метод `today()` аналогичен методу `now()`, <u>но рекомендуется использовать именно метод `now()`</u>.

* Метод `timestamp()` преобразует объект типа `datetime` в количество секунд, прошедших с начала эпохи (возвращает значение типа `float`), а метод `fromtimestamp()`, наоборот, преобразует количество секунд, прошедших с начала эпохи, в `datetime` (возвращает объект `datetime` в локальном часовом поясе).

* При работе с объектами типа `datetime` мы также можем использовать функции `min()`, `max()`, `sorted()` и операторы сравнения.

* Если нужно создать новый объект `datetime` на основании уже существующего, можно использовать метод `replace()`, который вернёт новый объект `datetime` с переданными изменёнными значениями атрибутов:
```py
from datetime import datetime

datetime1 = datetime(1992, 10, 6, 10, 12, 45)
datetime2 = datetime1.replace(year=1995, month=12)         
datetime3 = datetime1.replace(day=17, hour=14, minute=37)

print(datetime1)
print(datetime2)
print(datetime3)
```

```
# Вывод:
1992-10-06 10:12:45
1995-12-06 10:12:45
1992-10-17 14:37:45
```

---
### Форматирование `datetime`
Как и объекты типа `date` и `time`, `datetime` выводится в формате ISO 8601. Для преобразования в строку нужного формата нужно использовать старый-добрый метод `strftime()`:
```py
from datetime import datetime

my_datetime = datetime(2021, 8, 10, 18, 20, 34)

print(my_datetime)  # вывод в ISO формате
print(my_datetime.strftime('%d.%m.%y --- %H::%M::%S'))
print(my_datetime.strftime('%d/%m/%y'))
print(my_datetime.strftime('%A %d, %B %Y'))
print(my_datetime.strftime('%H:%M:%S'))
```

```
# Вывод:
2021-08-10 18:20:34
10.08.21 --- 18::20::34
10/08/21
Tuesday 10, August 2021
18:20:34
```

Аргументы, которые можно передать в `strftime()` уже были приведены в виде таблицы в параграфе "Форматирование даты и времени".

Если нужно получить представление даты-времени в ISO формате, можно воспользоваться методом `isoformat()` или функцией `str()`:
```py
from datetime import datetime

my_datetime = datetime(1992, 10, 6, 10, 12, 45)

print(my_datetime.isoformat())
print(str(my_datetime))
```

```
# Вывод:
1992-10-06T10:12:45
1992-10-06 10:12:45
```

---
### Преобразование строки в `datetime`
Преобразовать строку в объект типа `datetime` можно:
1. Вручную, используя `split()`;
2. С помощью метода `strptime()`.

Второй способ компактнее и читабельнее, поэтому поощряется в большей степени. Метод `strptime()` принимает два аргумента: первый – строку, второй – формат

{{< admonition info >}}
`strptime()` – это парсер, который превращает произвольную строку в объект типа `datetime`  
`strftime()` – это форматтер, который превращает объект `datetime` в строку заданного формата
{{< /admonition >}}

```py
from datetime import datetime

datetime0 = datetime.strptime('10.08.2034 13:55:59', '%d.%m.%Y %H:%M:%S')
datetime1 = datetime.strptime('10/08/21', '%d/%m/%y')
datetime2 = datetime.strptime('Tuesday 10, August 2021', '%A %d, %B %Y')
datetime3 = datetime.strptime('18.20.34', '%H.%M.%S')
datetime4 = datetime.strptime('2021/08/10', '%Y/%m/%d')
datetime5 = datetime.strptime('10.08.2021 (Tuesday, August)', '%d.%m.%Y (%A, %B)')
datetime6 = datetime.strptime('Year: 2021, Month: 08, Day: 10, Hour: 18.', 'Year: %Y, Month: %m, Day: %d, Hour: %H.')

print(datetime0, datetime1, datetime2, datetime3, datetime4, datetime5, datetime6, sep='\n')

print(my_datetime)
```

```
# Вывод:
2034-08-10 13:55:59
2021-08-10 00:00:00
2021-08-10 00:00:00
1900-01-01 18:20:34
2021-08-10 00:00:00
2021-08-10 00:00:00
2021-08-10 18:00:00
```

{{< admonition danger >}}
Первый аргумент должен соответствовать формату второго аргумента. Иначе возникнет исключение `ValueError`
{{< /admonition >}}

```py
from datetime import datetime

my_datetime = datetime.strptime('10/08/2034 13:55:59', '%d.%m.%Y %H:%M:%S')

print(my_datetime)

# Вывод: ValueError: time data '10/08/2034 13:55:59' does not match format '%d.%m.%Y %H:%M:%S'
```

{{< admonition info >}}
При создании объекта `datetime` из строки с помощью метода `strptime()` необязательно, чтобы строка содержала год, месяц и день, в отличие от ручного создания этого объекта
{{< /admonition >}}

---
## Тип данных `timedelta`
Тип данных `timedelta` представляет собой временной интервал (разница между двумя объектами `datetime` или `date`) и используется для удобного выполнения различных манипуляций над типами `datetime` или `date`. Он, как и предыдущие, неизменяем.

При создании объекта `timedelta` можно указать следующие аргументы: недели (`weeks`), дни (`days`), часы (`hours`), минуты (`minutes`), секунды (`seconds`), миллисекунды (`milliseconds`), микросекунды (`microseconds`). Все они являются необязательными и по умолчанию равны нулю.

```py
from datetime import timedelta

delta = timedelta(days=7, hours=20, minutes=7, seconds=17)

print(delta)
print(type(delta))
```

```
# Вывод:
7 days, 20:07:17
<class 'datetime.timedelta'>
```

{{< admonition info >}}
Аргументы могут быть целыми числами или же числами с плавающей точкой, кроме того они могут быть **отрицательными**
{{< /admonition >}}

`timedelta` хранит только `days`, `seconds`, `microseconds`, в то время как остальные переданные аргументы сводятся к ним по следующим правилам:
* `milliseconds` преобразуется в 1000 `microseconds`
* `minutes` в 60 `seconds`
* `hours` в 3600 `seconds`
* `weeks` в 7 `days`

Сами атрибуты `days`, `seconds` и `microseconds` имеют ограничения и нормализуются так, чтобы представление было уникальным:
- `0 <= microseconds < 1000000`
- `0 <= seconds < 3600*24` (количество секунд в одном дне)
- `-999999999 <= days <= 999999999`

Пример:
```py
from datetime import timedelta

delta1 = timedelta(days=50, seconds=27, microseconds=10, milliseconds=29000, minutes=5, hours=8, weeks=2)
delta2 = timedelta(weeks=1, hours=23, minutes=61)
delta3 = timedelta(hours=25)
delta4 = timedelta(minutes=300)

print(delta1, delta2, delta3, delta4, sep='\n')
```

```
# Вывод:
64 days, 8:05:56.000010
8 days, 0:01:00
1 day, 1:00:00
5:00:00
```

Также `timedelta` может представлять собой отрицательный временной интервал:
```py
from datetime import timedelta

delta1 = timedelta(minutes=-40)
delta2 = timedelta(seconds=-10, weeks=-2)

print(delta1)
print(delta2)
```

```
# Вывод:
-1 day, 23:20:00
-15 days, 23:59:50
```

В первом случае, если мы сложим -1 день с временем 23:20, то как раз получим введённые -40 минут. Аналогично и со вторым примером.

{{< admonition info >}}
Объект `timedelta` не хранит отрицательное число секунд и микросекунд, но может хранить отрицательное количество дней
{{< /admonition >}}

Если нужно получить из отрицательного значения `timedelta` положительное, можно использовать встроенную функцию `abs()`:
```py
from datetime import timedelta

delta = timedelta(days=-2, minutes=-300)
abs_delta = abs(delta)

print('Исходная:', delta.days, delta.seconds, delta, sep='\n')
print('С модулем:', abs_delta.days, abs_delta.seconds, abs_delta, sep='\n')
```

```
# Вывод:
Исходная:
-3
68400
-3 days, 19:00:00
С модулем:
2
18000
2 days, 5:00:00
```

Для преобразования `timedelta` к строковому типу можно использовать встроенные функции `str()` и `repr()` (первая вызывается автоматически при печати значения объекта этого типа):
```py
from datetime import timedelta

delta1 = timedelta(weeks=1, hours=23, minutes=61)
delta2 = timedelta(minutes=-300)

print(str(delta1), str(delta2), sep='\n')
print(repr(delta1), repr(delta2), sep='\n')
```

```
8 days, 0:01:00
-1 day, 19:00:00
datetime.timedelta(days=8, seconds=60)
datetime.timedelta(days=-1, seconds=68400)
```

### Метод `total_seconds()`
`total_seconds()` возвращает общее количество секунд, содержащееся во временном интервале `timedelta`

У типа `timedelta` нет атрибутов `hours`, `minutes` и `week`, поэтому, чтобы получить, к примеру, часы и минуты, можно воспользоваться следующим кодом:
```py
def hours_minutes(td):
    return td.seconds // 3600, (td.seconds // 60) % 60
```

---
### Сравнение временных интервалов
Объекты типа `timedelta` можно сравнивать между собой, как и любые другие. 

{{< admonition danger >}}
Но если временной интервал сравнивается с другим типом данных операторами `<, >, <=, >=`, возникает ошибка (исключение) `TypeError`.
{{< /admonition >}}

---
### Операции с `timedelta`
* Сложение и разность временных интервалов:
```py
from datetime import timedelta

delta1 = timedelta(days=5) + timedelta(seconds=3600)  # 5 дней + 1 час
delta2 = timedelta(days=5) - timedelta(seconds=3600)  # 5 дней - 1 час

print(delta1)
print(delta2)
```

```
# Вывод:
5 days, 1:00:00
4 days, 23:00:00
```

* Умножение интервала на число:
```py
from datetime import timedelta

delta1 = 48 * timedelta(hours=1)
delta2 = timedelta(weeks=1) * (3/7)

print(delta1)
print(delta2)
```

```
2 days, 0:00:00
3 days, 0:00:00
```

{{< admonition info >}}
При умножении интервала на `float` число может произойти округление!
{{< /admonition >}}

* Деление интервала на число
```py
from datetime import timedelta

delta = timedelta(hours=1, minutes=6)
delta1 = delta / 2
delta2 = delta // 5

print(delta1)
print(delta2)
```

```
# Вывод:
0:33:00
0:13:12
```

* Деление временных интервалов друг на друга. По сути общая длительность первого интервала делится на общую длительность второго (которые вычисляются методом `total_seconds()`):
```py
from datetime import timedelta

delta1 = timedelta(weeks=1) / timedelta(hours=5)   # обычное деление, результат float
delta2 = timedelta(weeks=1) // timedelta(hours=5)  # целочисленное деление, результат int

print(delta1)
print(delta2)
```

```
# Вывод:
33.6
33
```

Также можно найти остаток от деления двух интервалов, при этом вернётся объект `timedelta`:
```py
from datetime import timedelta

delta1 = timedelta(weeks=1) % timedelta(hours=5)    # 3 часа
delta2 = timedelta(hours=1) % timedelta(minutes=7)  # 4 минуты

print(delta1)
print(delta2)
```

```
# Вывод:
3:00:00
0:04:00
```

---
### Операции с `datetime` и `date`
К объектам типа `datetime` и `date` можно прибавлять (вычитать) временные интервалы, формируя новые объекты тех же типов данных:
```py
from datetime import datetime, date, timedelta

my_datetime1 = datetime(2021, 1, 1, 12, 15, 20) + timedelta(weeks=1, hours=25)
my_datetime2 = datetime(2021, 1, 1, 12, 15, 20) - timedelta(weeks=1, hours=25)

my_date1 = date(2021, 1, 1) + timedelta(hours=49)
my_date2 = date(2021, 1, 1) - timedelta(hours=49)

print(my_datetime1, my_datetime2, my_date1, my_date2, sep='\n')
```

```
# Вывод:
2021-01-09 13:15:20
2020-12-24 11:15:20
2021-01-03
2020-12-30
```

{{< admonition info >}}
При прибавлении `timedelta` к `data` неполные сутки отбрасываются
{{< /admonition >}}

При вычитании объектов `date` или `datetime` получается объект `timedelta`:
```py
from datetime import datetime, date, timedelta

delta1 = datetime(2021, 1, 1, 12, 15, 20) - datetime(2020, 5, 1, 10, 5, 10)
delta2 = date(2020, 2, 29) - date(2019, 9, 1)
delta3 = date(2019, 9, 1) - date(2020, 2, 29)

print(type(delta1))
print(type(delta2))
print(type(delta3))
```

```
# Вывод:
<class 'datetime.timedelta'>
<class 'datetime.timedelta'>
<class 'datetime.timedelta'>
```