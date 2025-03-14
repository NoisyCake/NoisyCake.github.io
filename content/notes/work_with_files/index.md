---
title: "Работа с txt файлами в Python"
date: 2024-05-17 00:00:01
type: posts
tags:
  - python
  - files
categories:
  - python
draft: false
---

Управление текстовыми и бинарными файлами в языке программирования Python
<!--more-->
Программы, которые мы писали до сих пор, требовали повторного ввода данных при каждом запуске, потому что, как только программа заканчивает свою работу, данные для переменных исчезают из оперативной памяти. Если их нужно сберечь между выполнениями программы, требуется запись. Данные записываются в файл, обычно сохраняющийся на диске компьютера.  
Сохраненные в файле данные обычно остаются в нем после завершения работы программы, их можно позже извлечь и использовать.

## Файловый ввод и вывод
Когда программе нужно сохранить данные для дальнейшего использования, она пишет их в файл. Из файла записанные данные можно считать.  

Программисты называют такой процесс сохранения данных – **запись данных** в файл. Когда часть данных пишется в файл, она копируется из переменной, находящейся в оперативной памяти. Файл, куда сохраняются данные, называется **файл вывода**, потому что программа помещает в него выходные данные.  

Процесс извлечения данных из файла называется **чтением данных** из файла. Данные считываются из **файла ввода**. Программа извлекает входные данные из этого файла. Когда порция данных считывается из файла, она копируется в оперативную память, где на нее ссылается переменная.

Когда в программе используется файл, как правило требуется выполнить три шага:
* **Открыть файл**. В процессе открытия файла создается связь между файлом и программой. Открытие файла вывода обычно создает файл на диске и позволяет программе записать в него данные. Открытие файла ввода позволяет программе прочитать данные из файла.
* **Обработать файл**. На этом шаге данные либо записываются в файл (если это файл вывода), либо считываются из файла (если это файл ввода).
* **Закрыть файл**. После использования файла программой его нужно закрыть, тем самым освободить ресурс и разорвать связь файла с программой.

### Типы файлов
Существует два типа файлов: **текстовые** и **двоичные (бинарные)**. Python позволяет работать с любыми из них.  

Текстовый файл содержит данные, которые были закодированы в виде текста при помощи такой схемы кодирования, как ASCII или Юникод. Даже если файл содержит числа, эти числа в файле хранятся как набор символов. В результате файл можно открыть и просмотреть в текстовом редакторе, таком как Блокнот.  

Двоичный файл содержит данные, которые не были преобразованы в текст. Данные, которые помещены в двоичный файл, предназначены только для чтения программой, и такой файл невозможно просмотреть в текстовом редакторе.  

---
### Методы доступа к файлам
Большинство языков программирования обеспечивают два способа получения доступа к данным в файле:
* **последовательный**,
* **прямой** или **произвольный**.

**Последовательный**, как при проигрывании кассет с записью на пленке, выдает порции информации одну за другой. При работе с таким файлом не получится перескочить сразу к нужной части данных, сначала придется прочитать все предыдущие. 

При работе с файлом с **прямым** или **произвольным** доступом можно перескочить непосредственно к любой порции данных, не читая предыдущие. Как проигрыватель компакт-дисков или МР3-плеер перескакивает сразу к любой песне.

---
### Кодировка файлов
Кодировка UTF-8 самая распространенная, рекомендуется использовать именно ее в качестве кодировки по умолчанию для текстовых файлов. Она довольно сложная, и на кодирование одного символа может уходить до 6 байт.  

В операционной системе Windows до сих пор используется однобайтовая кодировка Windows-1251. Чтобы избежать проблем при работе с текстовыми файлами в Windows нужно явно указывать кодировку.

## Работа с текстовыми файлами

### Открытие файла
Функция `open()` открывает файлы в Python. Она создает файловый объект и связывает его с файлом на диске.

Формат применения: `файловая_переменная = open(имя_файла, режим_доступа)`, где `файловая_переменная` – имя переменной, которая ссылается на файловый объект (такую переменную называют дескриптор файла); `имя_файла` – строковый литерал/ключ, задающий имя файла; `режим_доступа` – строковый литерал/ключ, задающий режим доступа.  

Ключи режима доступа:
|Ключ|Режим|Примечание|
|:-:|:-|:-|
|`'r'`|Чтение|В этом режиме файл не изменить|
|`'w'`|Запись|Если файл уже существует, содержимое стирается|
|`'a'`|Добавление|Открывает для записи. Добавляет данные в конец файла. <br>Если файла не существует, он будет создан|
|`'r+'`|Чтение и запись|Частичная перезапись содержимого файла|
|`'x'`|Создание|Если файл существует, произойдёт ошибка|

Ключи режима обработки файлов:
|Ключ|Режим|
|:-:|:-|
|`'t'`|Текстовый|
|`'b'`|Бинарный|

Чтобы открыть бинарный файл для чтения, можно воспользоваться следующим кодом: `file = open('file.dat', 'rb')`. 

По умолчанию режим доступа определён для чтения, поэтому, к примеру, файл `students.txt` можно открыть для чтения так: `student_file = open('students.txt')`.  
Аналогично, по умолчанию режим обработки определён как текстовый.

Предположим, надо создать файл с именем `sales.txt` и записать в него данные о продажах. Это можно сделать так:
`sales_file = open('sales.txt', 'w')`

---
### Указание места расположения файла
Когда в `open()` передается имя файла без указания пути, интерпретатор Python исходит из предположения, что место расположения файла то же, что у исполняемой программы 
(используется относительный путь).  

Если же требуется указать путь, то это можно сделать так: `test_file = open('C:\\Users\\temp\\test.txt', 'w')`. При этом вместо экранирования символа `\` можно 
воспользоваться "raw strings": `test_file = open(r'C:\Users\temp\test.txt', 'w')`.

---
### Кодировка
При открытии файлов следует указывать его кодировку. Например, если открывается файл на русском языке, нужно использовать параметр `encoding`: 
`file = open('info.txt', 'r', encoding='utf-8')`.

Чтобы получить кодировку уже открытого файла, используют файловое свойство `encoding`:
```py
file1 = open('students.txt', 'w')
file2 = open('customers.txt', 'w', encoding='utf-8')

print(file1.encoding)
print(file2.encoding)

file1.close()
file2.close()
```

```
# Вывод:
cp1252
utf-8
```

---
### Закрытие файлов
После окончания работы с файлом его необходимо закрыть по следующим причинам:
* если файл изменялся, это позволит корректно его сохранить;
* если открытый файл потребуется другим программам, программа на Python может его блокировать;
* не стоит держать в памяти лишние, уже не нужные, данные;
* удалить открытый кем-то файл проблематично.  

Для закрытия файла используется метод `close()`:
```py
file = open('info.txt', 'r')

file.close()
```

Чтобы проверить открыт файл или закрыт можно использовать файловое свойство (атрибут) `closed`:
```py
file1 = open('students.txt', 'w')
file2 = open('customers.txt', 'w')

file1.close()

print(file1.closed)
print(file2.closed)

file2.close() 
```

```
# Вывод:
True
False
```

---
### Чтение файлов
Для чтения содержимого открытого для чтения файла используются три файловых метода:

* `read()` – читает все содержимое файла;
* `readline()` – читает одну строку из файла;
* `readlines()` – читает все содержимое файла и возвращает список строк.

Предположим, в папке с исполняемой программой есть текстовый файл `languages.txt` с содержимым:
```
Python
Java
Javascript
C#
C
C++
PHP
R
Objective-C
```

#### Метод `read()` 
`read()` считывает все содержимое из файла и возвращает строку, которая может содержать символы перехода на новую строку `'\n'`.
Приведённый ниже код считывает содержимое файла `languages.txt` в переменную `content`. В который будет содержаться строка 
`'Python\nJava\nJavascript\nC#\nC\nC++\nPHP\nR\nObjective-C'`.  

При этом, если методу `read()` передать целочисленный параметр, то будет считано не более заданного количества символов. 
Например, считывать файл посимвольно можно при помощи метода `read(1)`.

#### Метод `readline()`
`readline()` считывает одну строку из файла (до символа конца строки `'\n'`), при этом возвращается считанная строка вместе с символом `'\n'`. 
Если считать строку не удалось – достигнут конец файла и больше строк в нем нет, возвращается пустая строка.  

Этот метод удобен, когда нужно управлять процессом чтения из файла, особенно если файл очень большой и его полное считывание может привести к нехватке памяти.  

Чтобы прочитать содержимое всего файла построчно, можно воспользоваться циклами `while` или `for` (второй предпочтительнее). Сам же файл позволяет итерироваться по строкам,
поэтому его можно использовать в качестве аргументов функции `map()`, к примеру, или перебрать его строки в цикле, не объявляя отдельную переменную с содержимым файла:
```py
file = open('languages.txt', 'r', encoding='utf-8')

for line in file:
    print(line.strip())
    
file.close()
```

#### Метод `readlines()`
`readlines()` считывает все строки из файла и возвращает список из всех считанных строк (одна строка – один элемент списка). 
При этом, каждая строка в списке заканчивается символом переноса строки `'\n'`.

Чтобы удалить символ `'\n'` можно использовать списочное выражение, лямбда-функцию или функцию `map()`: `languages = list(map(str.strip, file.readlines()))`

Если передать в функцию `list()` ссылку на файловый объект `list(file)`, получим тот же результат, что при вызове метода `file.readlines()`.

---
### Курсор в файле
Вызов методов `read()`, `readlines()`, `readline()` перемещает текущую позицию (курсор) туда, где завершилось чтение. Для методов `read()` и `readlines()` это конец файла, для метода `readline()` – следующая строка после прочитанной.

После завершения чтения всего файла не получится считать ни одного символа. Все последующие вызовы методов `read()` или `readline()` будут приводить к считыванию пустой строки.

Для повторного чтения данных из файла, можно:
* переоткрыть файл, тогда курсор снова попадёт в начало;
* переместить курсор с помощью файлового метода `seek()`.

#### Файловые методы `seek()` и `tell()`
`seek()` задаёт позицию курсора в байтах от начала файла. Чтобы перевести курсор в самое начало файла необходимо вызвать метод `seek()`, передав ему в качестве аргумента значение 0.

Пусть есть файл `languages.txt`:
```
Python
Java
Javascript
C#
C++
PHP
R
Objective-C
```

Тогда приведённый ниже код даст одинаковый вывод `Python`:
```py
file = open('languages.txt', 'r', encoding='utf-8')
line1 = file.readline()
file.seek(0)  # переводим курсор в самое начало
line2 = file.readline()

print(line1, line2)

file.close()
```

Метод `tell()` возвращает позицию курсора в байтах от начала файла:
```py
file = open('languages.txt', 'r', encoding='utf-8')
print(file.tell())
line1 = file.readline()
print(file.tell())

file.close()
```

```
# Вывод:
0
8
```

---
### Менеджер контекста
Чтобы автоматически закрывать файл сразу после окончания работы с ним и осуществлять закрытие даже при возникновении ошибки, можно воспользоваться **менеджером контекстов**.

Менеджер контекста – объект, реализующий одноименный протокол. Объекты, реализующие этот протокол, позволяют использовать следующий специальный синтаксис:
```py
with object as name:
    # Здесь нам доступен ресурс name.
    # Это тело with-блока.
# А здесь ресурс name уже освобождён, даже если в теле with-блока произошла ошибка.
```

Весь код в теле `with`-блока работает "в контексте". Чаще всего контекст подразумевает выделение некоего ресурса, например, файла. По выходу из контекста ресурс автоматически освобождается, даже если при выполнении блока возникло исключение. Как только закончится код, оформленный с отступами в `with`, это будет означать, что контекст закончился, и Python автоматически закроет файл.

Следующие два блока кода эквиваленты по результату выполнения:
```py
file = open('languages.txt', 'r', encoding='utf-8')

for line in file:
    print(line)

file.close()  # ручное закрытие файла

print('Файл закрыт')
```

```py
with open('languages.txt', 'r', encoding='utf-8') as file:
    for line in file:
        print(line)
# автоматическое закрытие файла
print('Файл закрыт')
```

С помощью менеджера контекста можно работать с несколькими файлами:
```py
with open('input.txt', 'r') as input_file, open('output.txt', 'w') as output_file:
    # обработка файлов
```

---
### Запись данных в файл
Для записи используются два файловых метода (файл должен быть открыт для записи – режимы `'w'`, `'а'`, `'r+'`, иначе произойдет ошибка):
* `write()` – записывает переданную строку в файл;
* `writelines()` – записывает переданный список строк в файл.

#### Метод `write()`
Рассмотрим текстовый файл `myfile.txt`, содержащий следующие строки:
```
First line of the file.
Second line of the file.
Third line of the file.
```

Если файл открыт в режиме `'w'`, то его содержимое сначала полностью стирается, а уже затем в него добавляются данные:
```py
with open('myfile.txt', 'w', encoding='utf-8') as file:
    file.write('Python and beegeek forever\n')
    file.write('We love stepik <3')

# Файл myfile.txt будет содержать следующее:
# Python and beegeek forever
# We love stepik <3
```

Если файл открыт в режиме `'a'`, то запись происходит в самый конец файла (результат очевиден).

Если файл открыт в режиме `'r+'`, то происходит частичная перезапись его содержимого:
```py
with open('myfile.txt', 'r+', encoding='utf-8') as file:
    file.write('Python and beegeek forever\n')
    file.write('We love stepik.')

# Файл myfile.txt будет содержать следующее:
# Python and beegeek forever
# We love stepik. file.
# Third line of the file.
```

Последовательные вызовы метода `write()` дописывают текст в конец файла. Приведенный ниже код создает файл `philosophers.txt` и записывает в него три строки текста:
```py
with open('philosophers.txt', 'w', encoding='utf-8') as file:
    file.write('Джoн Локк\n')
    file.write('Дэвид Хьюм\n')
    file.write('Эдмyнд Берк\n')
```

#### Метод `writelines()`
На практике часто приходится записывать в файл содержимое целого списка. Это можно сделать с помощью цикла или метода `writelines()`, что удобнее. Метод `writelines()` принимает в качестве аргумента список строк и записывает его в файл.

Приведенный ниже код создает файл `philosophers.txt` и записывает в него содержимое списка `philosophers`:
```py
philosophers = ['Джoн Локк\n', 'Дэвид Хьюм\n', 'Эдмyнд Берк\n']

with open('philosophers.txt', 'w', encoding='utf-8') as file:
    file.writelines(philosophers)
```

#### Запись в файл при помощи функции `print()`
Для записи данных в файл можно также использовать встроенную функцию `print()`. Для этого нужно передать ей еще один именованный аргумент `file`, указывающий на открытый файл. При этом функция `print()` автоматически добавляет переход на новую строку:
```py
with open('philosophers.txt', 'w', encoding='utf-8') as output:
    print('Джoн Локк', file=output)
    print('Дэвид Хьюм', file=output)
    print('Эдмyнд Берк', file=output)

# Файл будет содержать:
# Джoн Локк
# Дэвид Хьюм
# Эдмyнд Берк
```

---
### Немного про буфер
В некоторых операционных системах невыполнение операции закрытия файла может привести к потере данных. Данные сначала пишутся в буфер – небольшую область временного хранения в оперативной памяти. Когда буфер заполняется, система записывает его содержимое в файл. Это увеличивает производительность системы, потому что запись данных в оперативную память быстрее записи на диск. Процесс закрытия файла записывает любые несохраненные данные из буфера в файл. Чтобы принудительно записать содержимое буфера в файл, используется файловый метод `flush()`.