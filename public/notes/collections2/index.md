# Коллекции в Python. Часть II


![](feature.png)

Конспект посвящён модулю collections и его составляющим, а именно именованному кортежу, специфическим типам словарей и т.п. Вторая часть конспектов про типы данных в Python
&lt;!--more--&gt;
## Модуль collections

Python содержит встроенный модуль `collections`, который содержит специализированные типы коллекций, альтернативных традиционным `list`, `tuple`, `dict`:
* `namedtuple`
* `defaultdict`
* `OrderedDict`
* `Counter`
* `ChainMap`
* и прочие

Начнём с разбора именованных кортежей.

---
## Namedtuple
Для использования: `from collections import namedtuple`

Именованные кортежи (тип `namedtuple`) — это подтип обычных кортежей в Python. У них те же функции, что и у обычных, но их значения можно получать как с помощью индекса (например, `[0]`), так и с помощью имени через точку (например, `.name`). Их основным назначением является улучшение читаемости кода.

Опишем точку на плоскости, имеющую две координаты `x` и `y` с помощью именованного кортежа:
```py
from collections import namedtuple

Point = namedtuple(&#39;Point&#39;, [&#39;x&#39;, &#39;y&#39;])     # создаём подкласс кортежа Point (именованный кортеж)

point = Point(3, 7)                         # создаём экземпляр Point

print(point)
print(point.x, point.y)
print(point[0], point[1])
print(type(point))
```

```
# Вывод:
Point(x=3, y=7)
3 7
3 7
&lt;class &#39;__main__.Point&#39;&gt;
```

Как и обычный, именованный кортеж может содержать изменяемые значения, но стоит помнить, что такие кортежи не будут хэшируемы:
```py
from collections import namedtuple

Person = namedtuple(&#39;Person&#39;, [&#39;name&#39;, &#39;children&#39;])

sveta = Person(&#39;Sveta Ivanova&#39;, [&#39;Larisa&#39;, &#39;Keksik&#39;])
print(sveta)

sveta.children.append(&#39;Romka&#39;)
print(sveta)
```

```
# Вывод:
Person(name=&#39;Sveta Ivanova&#39;, children=[&#39;Larisa&#39;, &#39;Keksik&#39;])
Person(name=&#39;Sveta Ivanova&#39;, children=[&#39;Larisa&#39;, &#39;Keksik&#39;, &#39;Romka&#39;])
```

{{&lt; admonition danger &gt;}}
Очевидно, что подобный код `sveta.children = [&#39;Larisa&#39;, &#39;Keksik&#39;, &#39;Romka&#39;]` привёл бы к ошибке
{{&lt; /admonition &gt;}}

Создавать кортежи можно также с помощью именованных аргументов:
```py
from collections import namedtuple

Point = namedtuple(&#39;Point&#39;, [&#39;x&#39;, &#39;y&#39;])
point1 = Point(2, 4)                     # позиционные аргументы
point2 = Point(y=10, x=3)                # именованные аргументы

print(point1)
print(point2)
```

```
# Вывод:
Point(x=2, y=4)
Point(x=3, y=10)
```

При работе с именованными кортежами можно пользоваться срезами (результатом будет обычный кортеж)

### Функция `namedtuple()`
Эта фабричная функция (понятие из ООП) позволяет создавать подклассы кортежа с именованными полями (классы именованных кортежей).

Сигнатура: `namedtuple(typename, field_names, *, rename=False, defaults=None, module=None)`. Разберёмся с каждым параметров по-отдельности.

#### `typename` и `field_names`
Параметр `typename` отвечает за имя создаваемого класса `namedtuple` (который и возвращает функция `namedtuple()`), а параметр `fieldnames` за название полей, которые мы будем использовать, чтобы получить доступ к значениям определённого экземпляра именованного кортежа. В качестве параметра `field_names` можно использовать:
* Список.
```py
from collections import namedtuple

Point = namedtuple(&#39;Point&#39;, [&#39;x&#39;, &#39;y&#39;])  # передаем список
point =  Point(2, 4)
print(point)                             # выводит Point(x=2, y=4)
```

* Кортеж.
```py
from collections import namedtuple

Point = namedtuple(&#39;Point&#39;, (&#39;x&#39;, &#39;y&#39;))  # передаем кортеж
point =  Point(2, 4)
print(point)                             # выводит Point(x=2, y=4)
```

* Словарь. В этом случае для полей именованного кортежа используются ключи словаря, поэтому в качестве значений можно указать, все что угодно.
```py
from collections import namedtuple

Point = namedtuple(&#39;Point&#39;, {&#39;x&#39;: 0, &#39;y&#39;: 69})  # передаем словарь
point =  Point(2, 4)
print(point)                                    # выводит Point(x=2, y=4)
```

* Строка. При создании именованного кортежа с помощью строки мы указываем поля либо через символ пробела, либо разделяя их символом `,`.
```py
from collections import namedtuple

Point = namedtuple(&#39;Point&#39;, &#39;x y&#39;)  # передаем строку (можно &#39;x,y&#39;)
point =  Point(2, 4)
print(point)                        # выводит Point(x=2, y=4)
```

* Множество. Можно создать именованный кортеж с помощью множества, но делать это не рекомендуется, так как множество — неупорядоченный набор данных, поэтому поля могут перемешаться. 
```py
from collections import namedtuple

Point = namedtuple(&#39;Point&#39;, {&#39;x&#39;, &#39;y&#39;})  # в качестве второго параметра передаем множество
point =  Point(2, 4)
print(point)                             # выводит Point(x=2, y=4) или Point(y=2, x=4)
```

{{&lt; admonition tip &gt;}}
В качестве параметра `field_names` можно передавать любой итерируемый объект, например, результат вызова функций `map()` и `filter()`
{{&lt; /admonition &gt;}}

В качестве названия полей для именованных кортежей мы можем использовать любое корректное название имени переменной, за исключением:
* имён, начинающихся с символа `_`;
* ключевых слов языка Python (`if`, `with`, `else`, `class`, ...).

#### `rename`
При `rename=True` названия полей, переданных в `field_names`, которые содержат ключевые слова Python, переименовываются в соответствии с их порядковыми номерами (начиная с нуля), перед которыми ставится символ `_`. Посмотрим на пример:
```py
from collections import namedtuple

headers = (&#39;name&#39;, &#39;surname&#39;, &#39;age&#39;, &#39;class&#39;, &#39;with&#39;, &#39;color&#39;, &#39;name&#39;, &#39;class&#39;, &#39;if&#39;)

Student = namedtuple(&#39;Student&#39;, headers, rename=True)

stud = Student(&#39;Иван&#39;, &#39;Бережной&#39;, 19, 2, &#39;me&#39;, &#39;green&#39;, &#39;Cake&#39;, &#39;2A&#39;, &#39;else&#39;)
print(stud)

# Вывод: Student(name=&#39;Иван&#39;, surname=&#39;Бережной&#39;, age=19, _3=2, _4=&#39;me&#39;, color=&#39;green&#39;, _6=&#39;Cake&#39;, _7=&#39;2A&#39;, _8=&#39;else&#39;)
```

#### `defaults`
Параметр `defaults` (работает в Python 3.7&#43;) используется для того, чтобы установить значения по умолчанию для полей именованного кортежа. Можно указать значение по умолчанию только для некоторых полей, при этом `defaults` присваивает значения по умолчанию с хвоста.
```py
from collections import namedtuple

Point = namedtuple(&#39;Point&#39;, [&#39;x&#39;, &#39;y&#39;], defaults=(0, 0))
point = Point()      # используем значения по умолчанию

print(point)

# Вывод: Point(x=0, y=0)
```

#### `module`
Посмотрим на вывод следующего кода:
```py
from collections import namedtuple

Point = namedtuple(&#39;Point&#39;, [&#39;x&#39;, &#39;y&#39;])
point = Point(1, 2)
print(type(point))

# Вывод: &lt;class &#39;__main__.Point&#39;&gt;
```

Если мы укажем допустимое имя модуля для этого аргумента, тогда атрибуту `.__ module__` результирующего именованного кортежа будет присвоено это значение:
```py
from collections import namedtuple

Point = namedtuple(&#39;Point&#39;, [&#39;x&#39;, &#39;y&#39;], module=&#39;custom&#39;)
point = Point(1, 2)
print(point.__module__)

# Вывод: custom
```

{{&lt; admonition info &gt;}}
Параметр `module` был добавлен в Python 3.6 для того, чтобы появилась возможность сериализовать/десериализовать именованные кортежи с помощью модуля `pickle` в разных реализациях Python (IronPython, Jython и т.д.)
{{&lt; /admonition &gt;}}

---
### Распаковка именованного кортежа
Именованный кортеж распаковывается также, как и обычный:
```py
from collections import namedtuple

Person = namedtuple(&#39;Person&#39;, [&#39;name&#39;, &#39;age&#39;, &#39;height&#39;])

ivan = Person(&#39;Иван&#39;, 19, 179)

print(*ivan)

# Вывод: Иван 19 179
```

---
### Атрибуты `_fields` и `_field_defaults`
Именованные кортежи имеют два дополнительных атрибута: `_fields` и `_field_defaults`. Первый содержит кортеж строк, в котором перечислены имена полей. Второй атрибут содержит словарь, который сопоставляет имена полей с соответствующими значениями по умолчанию, если таковые имеются.
```py
from collections import namedtuple

Person = namedtuple(&#39;Person&#39;, [&#39;name&#39;, &#39;age&#39;, &#39;height&#39;])

ivan = Person(&#39;Иван&#39;, 19, 179)

print(ivan)
print(ivan._fields)
print(Person._fields)
```

```
# Вывод:
Person(name=&#39;Иван&#39;, age=19, height=179)
(&#39;name&#39;, &#39;age&#39;, &#39;height&#39;)
(&#39;name&#39;, &#39;age&#39;, &#39;height&#39;)
```

Как видно из примера выше, можно обращаться к атрибуту `_fields` как через переменную (`ivan`), так и через сам тип именованного кортежа (`Person`).

С помощью атрибута `_fields` можно создавать новые именованные кортежи на основании уже существующих. В следующем примере создаётся новый именованный кортеж с именем `ExtendedPerson`, который расширяет старый `Person` новым полем `weight`:
```py
from collections import namedtuple

Person = namedtuple(&#39;Person&#39;, [&#39;name&#39;, &#39;age&#39;, &#39;height&#39;])

ExtendedPerson = namedtuple(&#39;ExtendedPerson&#39;, [*Person._fields, &#39;weight&#39;])  # распаковка полей старого кортежа

ivan = ExtendedPerson(&#39;Иван&#39;, 19, 179, 63)

print(ivan)
print(ExtendedPerson._fields)
```

```
# Вывод:
ExtendedPerson(name=&#39;Иван&#39;, age=19, height=179, weight=63)
(&#39;name&#39;, &#39;age&#39;, &#39;height&#39;, &#39;weight&#39;)
```

Мы также можем использовать атрибут `_fields` для перебора полей и их значений с помощью встроенной функции `zip()`:
```py
from collections import namedtuple

Person = namedtuple(&#39;Person&#39;, [&#39;name&#39;, &#39;age&#39;, &#39;height&#39;])

ivan = Person(&#39;Иван&#39;, 19, 179)

for field, value in zip(Person._fields, ivan):
    print(field, &#39;-&gt;&#39;, value)
```

```
# Вывод:
name -&gt; Иван
age -&gt; 19
height -&gt; 179
```

С помощью атрибута `_field_defaults` мы можем выяснить, какие поля именованного кортежа имеют значения по умолчанию:
```py
from collections import namedtuple

Person = namedtuple(&#39;Person&#39;, [&#39;name&#39;, &#39;age&#39;, &#39;height&#39;, &#39;country&#39;], defaults=[&#39;Russia&#39;])

ivan = Person(&#39;Иван&#39;, 19, 179)

print(ivan)
print(ivan._field_defaults)
print(Person._field_defaults)
```

```
# Вывод:
Person(name=&#39;Иван&#39;, age=19, height=179, country=&#39;Russia&#39;)
{&#39;country&#39;: &#39;Russia&#39;}
{&#39;country&#39;: &#39;Russia&#39;}
```

{{&lt; admonition info &gt;}}
Если именованный кортеж не предоставляет значений по умолчанию, тогда атрибут `_field_defaults` содержит пустой словарь.
{{&lt; /admonition &gt;}}

---
### Методы `_make()`, `_replace()`, `_asdict()`
* `_make()` — метод класса (не экземпляра), работающий как альтернативный конструктор класса и возвращающий новый экземпляр именованного кортежа `typename`.
```py
from collections import namedtuple

Person = namedtuple(&#39;Person&#39;, [&#39;name&#39;, &#39;age&#39;, &#39;height&#39;])

ivan = Person._make([&#39;Ivan&#39;, 19, 179])

print(ivan)

# Вывод: Person(name=&#39;Ivan&#39;, age=19, height=179)
```

* `_asdict()` преобразует именованные кортежи в словари, в которых имена полей используются в качестве ключей. Ключи результирующего словаря находятся в том же порядке, что и поля в исходном именованном кортеже.
```py
from collections import namedtuple

Person = namedtuple(&#39;Person&#39;, [&#39;name&#39;, &#39;age&#39;, &#39;height&#39;])

ivan = Person._make([&#39;Иван&#39;, 19, 179])

print(ivan._asdict())

# Вывод: {&#39;name&#39;: &#39;Иван&#39;, &#39;age&#39;: 19, &#39;height&#39;: 179}
```

* `_replace()` создаёт новый именованный кортеж на основе уже существующего с заменой некоторых значений. Потребность в данном методе вызвана тем, что именованные кортежи являются неизменяемыми.
```py
from collections import namedtuple

Person = namedtuple(&#39;Person&#39;, [&#39;name&#39;, &#39;age&#39;, &#39;height&#39;, &#39;country&#39;])

ivan1 = Person(&#39;Иван&#39;, 19, 179, &#39;Russia&#39;)
ivan2 = ivan1._replace(age=20, country=&#39;Germany&#39;)

print(ivan1)
print(ivan2)
```

```
# Вывод:
Person(name=&#39;Иван&#39;, age=19, height=179, country=&#39;Russia&#39;)
Person(name=&#39;Иван&#39;, age=20, height=179, country=&#39;Germany&#39;)
```

---
### Схожесть со словарями
Функционал именованных кортежей можно полностью заменить функционалом словарей, тогда зачем вообще нужен этот `namedtuple`? Если коротко, то он более быстрый и занимает меньше места в памяти. Сравним эти показатели с помощью нехитрых программ.

Начнём с потребления памяти:
```py
from collections import namedtuple
from pympler import asizeof

Person = namedtuple(&#39;Person&#39;, [&#39;name&#39;, &#39;age&#39;, &#39;height&#39;])

ivan = Person(&#39;Иван&#39;, 19, 179)
ivan_dct = {&#39;name&#39;: &#39;Иван&#39;, &#39;age&#39;: 19, &#39;height&#39;: 179}

print(f&#39;Именованный кортеж: {asizeof.asizeof(ivan)} байт&#39;)
print(f&#39;Словарь: {asizeof.asizeof(ivan_dct)} байт&#39;)
```

```
# Вывод:
Именованный кортеж: 112 байт
Словарь: 304 байт
```

Теперь посмотрим на производительность:
```py
from collections import namedtuple
from time import perf_counter

def average_time(structure, test_func):
    time_measurements = []
    for _ in range(1_000_000):
        start = perf_counter()
        test_func(structure)
        end = perf_counter()
        time_measurements.append(end - start)
    return sum(time_measurements) / len(time_measurements) * int(10**9)

def time_dict(dictionary):
    &#39;name&#39; in dictionary
    &#39;missing_key&#39; in dictionary
    28 in dictionary.values()
    &#39;missing_value&#39; in dictionary.values()
    dictionary[&#39;age&#39;]

def time_namedtuple(named_tuple):
    &#39;name&#39; in named_tuple._fields
    &#39;missing_field&#39; in named_tuple._fields
    28 in named_tuple
    &#39;missing_value&#39; in named_tuple
    named_tuple.age

Person = namedtuple(&#39;Person&#39;, [&#39;name&#39;, &#39;age&#39;, &#39;height&#39;])

ivan = Person(&#39;Иван&#39;, 19, 179)
ivan_dct = {&#39;name&#39;: &#39;Иван&#39;, &#39;age&#39;: 19, &#39;height&#39;: 179}

print(f&#39;Именованный кортеж: {average_time(ivan, time_namedtuple)} наносекунд&#39;)
print(f&#39;Словарь: {average_time(ivan_dct , time_dict)} наносекунд&#39;)
```

```
# Вывод:
Именованный кортеж: 382 наносекунд
Словарь: 508 наносекунд
```

## Defaultdict

Для использования: `from collections import defaultdict`

Основная проблема при работе с обычными словарями — попытка получить доступ к несуществующему ключу, которая вызывает ошибку `KeyError`. С этим можно справиться через `setdefault()`, `get()` или проверкой наличия ключа в словаря (`try/except` или оператор `in`), а можно воспользоваться типом данных `defaultdict`.

Тип `defaultdict` ведет себя почти так же, как обычный словарь `dict`, но если мы попытаемся получить доступ (или изменить значение) по несуществующему ключу, то `defaultdict` автоматически создаст ключ и сгенерирует для него значение по умолчанию. Такое поведение делает этот тип данных удобным вариантом обработки недостающих ключей в словарях.

`defaultdict` является является наследником класса `dict`, а значит обладает всеми его методами. 

Функция `defaultdict()` принимает в качестве аргумента тип элемента по умолчанию. Таким образом, для ключей, к которым происходит обращение, словарь defaultdict поставит в соответствие дефолтный элемент данного типа:
* `int` – число `0`;
* `float` – число `0.0`;
* `bool` – значение `False`;
* `str` – пустая строка `&#39;&#39;`;
* `list` – пустой список `[]`;
* `tuple` – пустой кортеж `()`;
* `set` – пустое множество `set()`;
* `dict` – пустой словарь `{}`.

```py
from collections import defaultdict

info = defaultdict(int)       # создаем словарь со значением по умолчанию 0

info[&#39;name&#39;] = &#39;Ivan&#39;
info[&#39;age&#39;] = 19
info[&#39;job&#39;] = &#39;Darkstore&#39;

print(info[&#39;salary&#39;])
print(info)
```

```
# Вывод:
0
defaultdict(&lt;class &#39;int&#39;&gt;, {&#39;name&#39;: &#39;Ivan&#39;, &#39;age&#39;: 19, &#39;job&#39;: &#39;Darkstore&#39;, &#39;salary&#39;: 0})
```

Помимо первого аргумента — типа элемента по умолчанию — мы можем передать второй аргумент: словарь, на основании которого будет создан `defaultdict`.
```py
from collections import defaultdict

info = defaultdict(int, {&#39;name&#39;: &#39;Ivan&#39;, &#39;age&#39;: 19, &#39;job&#39;: &#39;Darkstore&#39;})

print(info[&#39;name&#39;])
print(info[&#39;salary&#39;])
print(info)
```

```
# Вывод:
Ivan
0
defaultdict(&lt;class &#39;int&#39;&gt;, {&#39;name&#39;: &#39;Ivan&#39;, &#39;age&#39;: 19, &#39;job&#39;: &#39;Darkstore&#39;, &#39;salary&#39;: 0})
```

Также допустимы все способы, которые мы используем при создании обычных словарей, а именно передача именованных аргументов или итерируемого объекта, содержащего пары ключ-значение (например, список кортежей). Правда, следующий код приведёт к ошибке, так как в качестве первого аргумента должен быть указан тип элемента по умолчанию, а не итерируемый объект с парами ключ-значение:
```py
from collections import defaultdict

info = defaultdict([(&#39;name&#39;, &#39;Ivan&#39;), (&#39;age&#39;, 19), (&#39;job&#39;, &#39;Darkstore&#39;)])

print(info)
```

Если же создать экземпляр `defaultdict` словаря без указания `default_factory` (значения по умолчанию для отсутствующих ключей), то поведение `defaultdict` будет таким же, как и у обычного словаря (тип `dict`). То же самое произойдёт, если передать значение `None` (является значением по умолчанию для `default_factory`) Следующий код вызовет ошибку `KeyError`:
```py
from collections import defaultdict

data = defaultdict()    # или data = defaultdict(None)

print(data[&#39;salary&#39;])
```

Если в ходе написания кода потребуется изменить значение по умолчанию, это можно осуществить через атрибут `default_factory`:
```py
from collections import defaultdict

data = defaultdict(int)
print(data[&#39;salary1&#39;])

data.default_factory = list
print(data[&#39;salary2&#39;])

data.default_factory = float
print(data[&#39;salary3&#39;])
```

```
# Вывод:
0
[]
0.0
```

При создании `defaultdict` словаря можно указывать не только тип данных для значений по умолчанию, но и любую функцию, **не принимающую аргументов и возвращающую некоторое дефолтное значение**.

Передадим функцию, объявленную с помощью `def`:
```py
from collections import defaultdict

def get_default():
    return 69

info = defaultdict(get_default, {&#39;name&#39;: &#39;Ivan&#39;, &#39;age&#39;: 19, &#39;job&#39;: &#39;Darkstore&#39;})

print(info[&#39;name&#39;])
print(info[&#39;salary&#39;])
```

```
# Вывод:
Ivan
69
```

Теперь передадим lambda-функцию:
```py
from collections import defaultdict

info = defaultdict(lambda: &#39;1000000$&#39;, {&#39;name&#39;: &#39;Ivan&#39;, &#39;age&#39;: 19, &#39;job&#39;: &#39;Darkstore&#39;})

print(info[&#39;name&#39;])
print(info[&#39;salary&#39;])
```

```
# Вывод:
Ivan
1000000$
```

Рассмотрим задачу и решим её с помощью `defaultdict`: пусть задан список чисел numbers, в котором некоторые числа встречаются несколько раз. Нужно узнать, сколько именно раз встречается каждое из чисел.
```py
from collections import defaultdict

numbers = [9, 8, 32, 1, 10, 1, 10, 23, 1, 4, 10, 4, 2, 2, 2, 2, 1, 10, 1, 2, 2, 32, 23, 23]
result = defaultdict(int)

for num in numbers:
    result[num] &#43;= 1
```

`defaultdict` часто используют в связке с пустым списком в качестве значения по умолчанию, чтобы начинать добавление элементов без лишнего кода.
```py
from collections import defaultdict

my_dict = defaultdict(list)

for i in range(7):
    my_dict[i].append(i)

for key in my_dict:
    print(key, my_dict[key])
```

```
# Вывод:
0 [0]
1 [1]
2 [2]
3 [3]
4 [4]
5 [5]
6 [6]
```

Таким образом, при использовании `defaultdict` нет необходимости ни проверять наличие соответствующих ключей в словаре, ни создавать предварительно пустые списки.

Python позволяет сравнивать `defaultdict` и `dict`:
```py
from collections import defaultdict

info1 = {&#39;name&#39;: &#39;Ivan&#39;, &#39;age&#39;: 19, &#39;job&#39;: &#39;Darkstore&#39;}
info2 = defaultdict(int, {&#39;name&#39;: &#39;Ivan&#39;, &#39;age&#39;: 19, &#39;job&#39;: &#39;Darkstore&#39;})

print(info1 == info2)

# Вывод: True
```

### Когда использовать `defaultdict`?
Есть несколько рекомендаций:
1. Если ваш код в значительной степени основан на словарях и вы все время имеете дело с отсутствующими ключами, вам следует подумать об использовании `defaultdict`, а не обычного `dict`;
2. Если элементы вашего словаря необходимо инициализировать некоторым значением по умолчанию, вам следует подумать об использовании `defaultdict`, вместо `dict`;
3. Если ваш код использует словари для агрегирования, накопления, подсчета или группировки значений, вам следует подумать об использовании `defaultdict`, вместо `dict`.

К слову, тип `defaultdict` работает быстрее чем использование методов `setdefault()` и `get()` обычного словаря.

## OrderedDict

Для использования: `from collections import OrderedDict`

Для использования: `from collections import OrderedDict`

В Python 3.6 словари были переработаны так, чтобы повысилась их производительность (и понизилось потребление памяти на 20-25%). Следствием такой переработки явился один очень интересный побочный эффект — словари стали упорядоченными, то есть стали сохранять порядок вставки элементов, хотя на тот момент официально этот порядок не гарантировался. «Официально не гарантируется» означает, что это была просто деталь реализации, которая могла быть изменена в будущих версиях Python. Но начиная с Python 3.7, в спецификации языка гарантируется сохранение порядка вставки элементов в словарь.

Задолго до переработки устройства словарей в рамках релиза Python 3.1 в стандартную библиотеку был добавлен тип `OrderedDict`, который на тот момент решал проблему неупорядоченности обычных словарей.

Стоит учесть, что в `OrderedDict` стоит использовать только для обратной совместимости со старыми программами на версиях Python, когда `dict` ещё не был упорядочен, ведь обычный словарь быстрее на 40% и занимает на 50% меньше памяти.

Таблица различий и особенностей классов `dict` и `OrderedDict`:
|Функционал|`OrderedDict|dict|
|:--|:--|:--|
|Сохранность порядка вставки ключей|Да (начиная с Python 3.1)|Да (начиная с Python 3.6)|
|Удобочитаемость и сигнализация о намерениях|Высокая|Низкая|
|Возможность менять порядок элементов|Да (метод `move_to_end()`)|Нет|
|Производительность операций|Низкая|Высокая|
|Потребление памяти|Высокое|Низкое|
|Учет порядка элементов при сравнении на равенство|Да|Нет|
|Перебор ключей в обратном порядке|Да (начиная с Python 3.5)|Да (начиная с Python 3.8)|
|Возможность добавления пользовательских атрибутов|Да (атрибут `.__dict__`)|Нет|
|Возможность использовать операторы `\|` и `\|=`|Да (начиная с Python 3.9)|Да (начиная с Python 3.9)|

`OrderedDict` является подклассом `dict`, а значит обладает всеми его методами, также имея собственные методы.

Как и `defaultdict`, эти словари можно создавать любым из доступных способов, как и обычные словари:
```py
from collections import OrderedDict

numbers1 = OrderedDict({&#39;one&#39;: 1, &#39;two&#39;: 2, &#39;three&#39;: 3})
numbers2 = OrderedDict({(&#39;one&#39;, 1), (&#39;two&#39;, 2), (&#39;three&#39;, 3)})
numbers3 = OrderedDict(one=1, two=2, three=3)
```

В большинстве случаев `OrderedDict` ведёт себя как и обычный словарь: программист может добавлять, обновлять, удалять, перебирать (по `keys()`, `values()` и `items()`) элементы, объединять словари с помощью операторов `|` и `|=` (конкатенация словарей). Словарь (обычный или `OrderedDict`) можно &#34;развернуть&#34; функцией `reversed()`.  
Обновляя значение ключа, позиция сохраняется, удаляя ключ и снова добавляя его, он помещается в конец словаря.

{{&lt; admonition info &gt;}}
Значение по ключу можно обновить как через квадратные скобки, так и через словарный метод `update()`
{{&lt; /admonition &gt;}}

### Метод `move_to_end()`
Метод `move_to_end()` позволяет переместить существующий элемент либо в конец, либо в начало словаря.
Ему можно передать два аргумента:
* `key` (обязательный) – ключ, который идентифицирует перемещаемый элемент
* `last=True` (необязательный) – логическое значение (тип `bool`), которое определяет, в какой конец словаря мы перемещаем элемент, значение `True` (по умолчанию) перемещает элемент в конец, значение `False` – в начало
```py
from collections import OrderedDict

numbers = OrderedDict(one=1, two=2, three=3)
print(numbers)

numbers.move_to_end(&#39;one&#39;)       # last=True
print(numbers)

numbers.move_to_end(&#39;three&#39;, last=False)       # last=False
print(numbers)
```

```
# Вывод:
OrderedDict({(&#39;one&#39;, 1), (&#39;two&#39;, 2), (&#39;three&#39;, 3)})
OrderedDict({(&#39;two&#39;, 2), (&#39;three&#39;, 3), (&#39;one&#39;, 1)})
OrderedDict({(&#39;three&#39;, 3), (&#39;two&#39;, 2), (&#39;one&#39;, 1)})
```

С помощью метода `move_to_end()` можно сортировать `OrderedDict` по ключам:
```py
from collections import OrderedDict

letters = OrderedDict(b=2, d=4, a=1, c=3)

for key in sorted(letters):
    letters.move_to_end(key)

print(letters)

# Вывод: OrderedDict({(&#39;a&#39;, 1), (&#39;b&#39;, 2), (&#39;c&#39;, 3), (&#39;d&#39;, 4)})
```

### Метод `popitem()`
Метод `popitem(last=True)` по умолчанию удаляет и возвращает элемент в порядке LIFO (Last-In/First-Out, последний пришел/первый ушел), то есть удаляет элементы с конца словаря.
```py
from collections import OrderedDict

numbers = OrderedDict(one=1, two=2, three=3)

print(numbers.popitem())
print(numbers)

print(numbers.popitem())
print(numbers)
```

```
# Вывод:
(&#39;three&#39;, 3)
OrderedDict({(&#39;one&#39;, 1), (&#39;two&#39;, 2)})
(&#39;two&#39;, 2)
OrderedDict({(&#39;one&#39;, 1)})
```

### Атрибут `.__dict__`
`OrderedDict` словари содержат дополнительный атрибут `__dict__`, которого нет у обычного словаря. Данный атрибут используется для динамического наделения объектов дополнительным функционалом. Этот атрибут позволяет динамически добавлять пользовательские атрибуты в `OrderedDict`. Использовать его можно двумя способами:
1. В стиле словаря: `ordered_dict.__dict__[&#39;attr&#39;] = value`;
2. Через точечную нотацию: `ordered_dict.attr = value`.

1:
```py
from collections import OrderedDict

letters = OrderedDict(b=2, d=4, a=1, c=3)
print(letters)
print(letters.__dict__)

letters.__dict__[&#39;advanced&#39;] = &#39;144&#39;
print(letters)
print(letters.__dict__)
```

```
# Вывод:
OrderedDict({(&#39;b&#39;, 2), (&#39;d&#39;, 4), (&#39;a&#39;, 1), (&#39;c&#39;, 3)})
{}
OrderedDict({(&#39;b&#39;, 2), (&#39;d&#39;, 4), (&#39;a&#39;, 1), (&#39;c&#39;, 3)})
{&#39;advanced&#39;: &#39;144&#39;}
```

2:
```py
from collections import OrderedDict

letters = OrderedDict(b=2, d=4, a=1, c=3)

letters.sorted_keys = lambda: sorted(letters.keys())

print(letters)
print(letters.sorted_keys())

letters[&#39;e&#39;] = 5
print(letters)
print(letters.sorted_keys())

for key in letters.sorted_keys():
    print(key, &#39;-&gt;&#39;, letters[key])
```

```
# Вывод:
OrderedDict({(&#39;b&#39;, 2), (&#39;d&#39;, 4), (&#39;a&#39;, 1), (&#39;c&#39;, 3)})
[&#39;a&#39;, &#39;b&#39;, &#39;c&#39;, &#39;d&#39;]
OrderedDict({(&#39;b&#39;, 2), (&#39;d&#39;, 4), (&#39;a&#39;, 1), (&#39;c&#39;, 3), (&#39;e&#39;, 5)})
[&#39;a&#39;, &#39;b&#39;, &#39;c&#39;, &#39;d&#39;, &#39;e&#39;]
a -&gt; 1
b -&gt; 2
c -&gt; 3
d -&gt; 4
e -&gt; 5
```

Если же методу передать необязательный аргумент `last=False`, то он начнет удалять и возвращать элементы в порядке FIFO (First-In/First-Out, первый пришел/первый ушел).

### Сравнение словарей
Стоит просто запомнить три истины:
* При сравнении на равенство обычных словарей порядок расположения их элементов **неважен**.
* При сравнении на равенство `dict` и `OrderedDict` словарей порядок расположения их элементов **неважен**.
* При сравнение на равенство `OrderedDict` словарей порядок расположения их элементов **важен**.

---
## Counter

Для использования: `from collections import Counter`

`Counter` является подклассом `dict`, специально разработанным для подсчёта хэшируемых объектов в Python. Он хранит объекты в качестве ключей, а их количество — в качестве значений. Для подсчёта класс `Counter` использует высокооптимизированную функцию, написанную на языке C.

{{&lt; admonition info &gt;}}
С помощью типа `Counter` можно реализовать концепцию мультимножества
{{&lt; /admonition &gt;}}

Есть несколько способов создать объект `Counter`. Например, можно передать коллекцию или итератор в конструктор:
```py
from collections import Counter

counter = Counter(&#39;mississippi&#39;)
print(counter)

# Вывод: Counter({&#39;i&#39;: 4, &#39;s&#39;: 4, &#39;p&#39;: 2, &#39;m&#39;: 1})
```

{{&lt; admonition info &gt;}}
`Counter` выдает результат в порядке убывания количества вхождений объектов
{{&lt; /admonition &gt;}}

Также при создании объекта `Counter` можно задавать начальные значения количества объектов:
```py
from collections import Counter

counter1 = Counter({&#39;i&#39;: 4, &#39;s&#39;: 4, &#39;p&#39;: 2, &#39;m&#39;: 1})
counter2 = Counter(i=4, s=4, p=2, m=1)

print(counter1)
print(counter2)
```

```
# Вывод:
Counter({&#39;i&#39;: 4, &#39;s&#39;: 4, &#39;p&#39;: 2, &#39;m&#39;: 1})
Counter({&#39;i&#39;: 4, &#39;s&#39;: 4, &#39;p&#39;: 2, &#39;m&#39;: 1})
```

Класс `Counter`, будучи подклассом типа `dict`, наследует все методы, предоставляемые обычным словарем. Но вызов метода `fromkeys()` всегда будет приводить к возникновению ошибки. Такое поведение не случайно, оно позволяет избежать ошибок неоднозначности при создании объектов типа `Counter`, например следующий код: `counter = Counter.fromkeys(&#39;mississippi&#39;, 2)` мог бы создать объект типа `Counter` на основе строки `mississippi` со значением по умолчанию равным 2 для всех символов строки, несмотря на реальное количество вхождений символов в строке `mississippi`.

Как и в обычных словарях, ключи в объектах `Counter` должны быть хэшируемы. Ограничений на тип значений нет, но для нормальной работы подсчёта объектов в качестве значений должны быть целые неотрицательные числа.

Доступ к элементам и итерирование по `Counter` словарям работает так же, как и у обычных словарей. Мы можем перебирать ключи напрямую или можем использовать словарные методы `items()`, `keys()` и `values()`. При этом, если обратиться по ключу, которого нет в `Counter` словаре, то ошибка `KeyError` возникать **не будет**. Будет возвращено нулевое значение (ключ создан не будет).

Объекты типа `Counter` можно сравнивать между собой. Очевидно, что одинаковыми будут те, что имеют одинаковые элементы (ключ: значение). Также до версии Python 3.10 словари `Counter(i=4)` и `Counter(i=4, s=0)` считались разными, но, начиная с Python 3.10 сравнение рассматривает отсутствующие элементы как имеющие нулевое значение, поэтому следующий код вернёт `True`:
```py
from collections import Counter

counter1 = Counter(i=4)
counter2 = Counter(i=4, s=0)

print(counter1 == counter2)
```

Объекты класса `Counter`, аналогично объектам `OrderedDict`, содержат дополнительный атрибут `__dict__`, который используется для динамического наделения объектов дополнительным функционалом:
```py
from collections import Counter

counter = Counter(green=10, red=25, blue=5)

print(counter.__dict__)

counter.__dict__[&#39;min_value&#39;] = lambda: min(counter.values())
counter.max_value = lambda: max(counter.values())

print(counter.min_value())
print(counter.max_value())
```

```
# Вывод:
{}
5
25
```

### Метод `update()`
Для изменения объектов типа `Counter` рекомендуется использовать метод `update()`. Он не заменяет значения как у обычных словарей, а суммирует существующие. При этом для новых объектов `update()` создаёт новые пары `ключ: количество`.
```py
from collections import Counter

letters = Counter({&#39;i&#39;: 4, &#39;s&#39;: 4, &#39;p&#39;: 2, &#39;m&#39;: 1})
letters.update(&#39;missouri&#39;)
print(letters)

# Вывод: Counter({&#39;i&#39;: 6, &#39;s&#39;: 6, &#39;p&#39;: 2, &#39;m&#39;: 2, &#39;o&#39;: 1, &#39;u&#39;: 1, &#39;r&#39;: 1})
```

Метод `update()` принимает любой итерируемый объект: список, строку, кортеж и т.д., другой объект типа `Counter`, либо обычный словарь. Также его можно использовать с именованными аргументами, например следующие две строки кода равнозначны: `sales.update(apple=3, orange=12, banana=7)` и `sales.update(monday_sales)`.
```py
from collections import Counter

sales = Counter(apple=20, orange=5, banana=10)
monday_sales = Counter(apple=3, orange=12, banana=7)
tuesday_sales = {&#39;apple&#39;: 4, &#39;orange&#39;: 5, &#39;tomato&#39;: 6}

print(sales)

sales.update(monday_sales)
print(sales)

sales.update(tuesday_sales)
print(sales)
```

```
# Вывод:
Counter({&#39;apple&#39;: 20, &#39;banana&#39;: 10, &#39;orange&#39;: 5})
Counter({&#39;apple&#39;: 23, &#39;orange&#39;: 17, &#39;banana&#39;: 17})
Counter({&#39;apple&#39;: 27, &#39;orange&#39;: 22, &#39;banana&#39;: 17, &#39;tomato&#39;: 6})
```

Если значения по ключам будут иметь тип, отличный от `int`, но работающий с оператором `&#43;`, то то ошибки при вызове метода `update()` не возникнет:
```py
from collections import Counter

counter1 = Counter(i=4, s=&#39;4&#39;)
counter2 = Counter(i=5, s=&#39;5&#39;)

counter1.update(counter2)

print(counter1)

# Вывод: Counter({&#39;i&#39;: 9, &#39;s&#39;: &#39;54&#39;})
```

Из предыдущего кода также можем заметить, что под ключом `&#39;s&#39;` содержится не `&#39;45&#39;`, а `&#39;54&#39;`. Так устроен метод `update()` — он складывает значение переданного объекта с изменяемым, но не наоборот, то есть `&#39;5&#39; &#43; &#39;4&#39;`.

---
### Метод `most_common()`
`most_common()` возвращает список наиболее повторяемых элементов и количество каждого из них в виде кортежей `(ключ, число повторений)`.
```py
from collections import Counter

letters = Counter(&#39;mississippi&#39;)
numbers = Counter([5, 6, 7, 1, 3, 9, 9, 1, 2, 5, 5, 7, 7, 9])

print(letters.most_common())
print(numbers.most_common())
```

```
# Вывод:
[(&#39;i&#39;, 4), (&#39;s&#39;, 4), (&#39;p&#39;, 2), (&#39;m&#39;, 1)]
[(5, 3), (7, 3), (9, 3), (1, 2), (6, 1), (3, 1), (2, 1)]
```

Если методу `most_common()` передать целочисленный аргумент `n`, то он вернет `n` самых часто повторяющихся элементов.
```py
from collections import Counter

letters = Counter(&#39;mississippi&#39;)
print(letters.most_common(2))

# Вывод: [(&#39;i&#39;, 4), (&#39;s&#39;, 4)]
```

Для поиска самых редких элементов, можно использовать срезы с отрицательным шагом.

---
### Метод `elements()`
`elements()` возвращает итератор по элементам, в котором каждый элемент повторяется столько раз, во сколько установлено его значение. Элементы возвращаются в порядке их появления. При этом, если количество элементов по некоторому ключу меньше единицы, то метод `elements()` просто проигнорирует его.
```py
from collections import Counter

letters = Counter(&#39;mississippi&#39;)
numbers = Counter([5, 6, 7, 1, 3, 9, 9, 1, 2, 5, 5, 7, 7, 9])

print(list(letters.elements()))
print(list(numbers.elements()))
```

```
# Вывод:
[&#39;m&#39;, &#39;i&#39;, &#39;i&#39;, &#39;i&#39;, &#39;i&#39;, &#39;s&#39;, &#39;s&#39;, &#39;s&#39;, &#39;s&#39;, &#39;p&#39;, &#39;p&#39;]
[5, 5, 5, 6, 7, 7, 7, 1, 1, 3, 9, 9, 9, 2]
```

### Метод `total()`
В Python 3.10 появился метод `total()`, который вычисляет сумму всех значений `Counter` словаря, включая отрицательные.
```py
from collections import Counter

letters = Counter(i=4, s=4, a=0, p=2, b=-98, m=1)

print(letters.total())

# Вывод: -87
```

---
### Метод `subtract()`
`subtract()` вычитает из значений элементов одного словаря `Counter` значения элементов другого словаря. Этот метод подобен `update()`, но вычитает количества, а не складывает их. При этом у результирующего словаря значения ключей могут быть нулевыми или отрицательными.

```py
from collections import Counter

counter1 = Counter(i=4, s=40, a=1, p=20, b=98, z=69)
counter2 = Counter(i=2, s=20, a=6, p=12, m=1, z=69)

counter1.subtract(counter2)

print(counter1)

# Вывод: Counter({&#39;b&#39;: 98, &#39;s&#39;: 20, &#39;p&#39;: 8, &#39;i&#39;: 2, &#39;z&#39;: 0, &#39;m&#39;: -1, &#39;a&#39;: -5})
```

Помимо словарей, метод `subtract()` может принимать любой итерируемый объект: список, строку, кортеж и т.д., а также его можно использовать с именованными аргументами.
```py
from collections import Counter

counter = Counter(i=4, s=40, a=1, p=20, b=98, z=69)
letters = &#39;iisssssapppz&#39;

counter.subtract(letters)       # обновляем значения в counter

print(counter)

# Вывод: Counter({&#39;b&#39;: 98, &#39;z&#39;: 68, &#39;s&#39;: 35, &#39;p&#39;: 17, &#39;i&#39;: 2, &#39;a&#39;: 0})
```

---
### Операторы `&#43;, -, &amp;, |`
Как мы уже знаем, методы `update()` и `subtract()` объединяют `Counter` словари путем сложения и вычитания количества соответствующих элементов. Python предоставляет удобные операторы сложения (`&#43;`) и вычитания (`-`), которые могут заменить вызовы данных методов. При использовании этих операторов из результирующего словаря исключаются элементы с нулевыми и отрицательными значениями.
```py
from collections import Counter

counter1 = Counter(i=10, s=40, p=10, m=1)
counter2 = Counter(i=2, s=8, p=10, m=3)

print(counter1 &#43; counter2)
print(counter1 - counter2)
print(counter2 - counter1)
```

```
# Вывод:
Counter({&#39;s&#39;: 48, &#39;p&#39;: 20, &#39;i&#39;: 12, &#39;m&#39;: 4})
Counter({&#39;s&#39;: 32, &#39;i&#39;: 8})
Counter({&#39;m&#39;: 2})
```

{{&lt; admonition info &gt;}}
Операторы `&#43;` и `-` работают только с `Counter` словарями, в то время как методы `update()` и `subtract()` — с любым итерируемым объектом
{{&lt; /admonition &gt;}}

`Counter` позволяет также использовать унарные операторы сложения и вычитания. В первом случае мы получаем новый `Counter` словарь, который содержит только элементы с положительными значениями, во втором — элементы с отрицательными значениями. Другими словами, операторы унарного сложения и вычитания прибавляют пустой Counter словарь или вычитают исходный из пустого. Следующие два блока кодов равнозначны:
```py
from collections import Counter

counter = Counter(a=5, b=-9, c=0)

print(&#43;counter)
print(-counter)
```

```py
from collections import Counter

counter = Counter(a=5, b=-9, c=0)

print(&#43;counter)
print(-counter)
```
и выводят:
```
# Вывод:
Counter({&#39;a&#39;: 5})
Counter({&#39;b&#39;: 9})
```

Помимо указанных выше операторов, Python также предоставляет операторы пересечения (`&amp;`) и объединения (`|`), которые возвращают минимум и максимум из соответствующих значений.
```py
from collections import Counter

counter1 = Counter(i=10, s=40, p=10, m=1)
counter2 = Counter(i=2, s=8, p=10, m=3)

print(counter1 &amp; counter2)
print(counter1 | counter2)
```

```
# Вывод:
Counter({&#39;p&#39;: 10, &#39;s&#39;: 8, &#39;i&#39;: 2, &#39;m&#39;: 1})
Counter({&#39;s&#39;: 40, &#39;i&#39;: 10, &#39;p&#39;: 10, &#39;m&#39;: 3})
```

---
## ChainMap

Для использования: `from collections import ChainMap`

`ChainMap` представляет из себя объединение нескольких словарей. Этот объект группирует словари вместе, что позволяет рассматривать их как единое целое.

`ChainMap` был добавлен в модуль `collections` в версии Python 3.3. Этот класс не создаёт новый словарь, вместо этого он хранит ссылки на исходные словари в списке, что, грубо говоря, позволяет программисту иметь несколько одноимённых ключей в одном словаре. 

Раз `ChainMap` содержит ссылки на объекты, то изменение содержания любого словаря, на основании которого создан `ChainMap`, изменяет и сам `ChainMap` объект. Аналогично, изменение `ChainMap` объекта приводит к изменению словаря, на основании которого он создан.
 
Чтобы создать объект `ChainMap`, можно, например, передать в конструктор словари:
```py
from collections import ChainMap

empty_chain_map = ChainMap()
print(empty_chain_map)

numbers = {&#39;one&#39;: 1, &#39;two&#39;: 2}
letters = {&#39;a&#39;: &#39;A&#39;, &#39;b&#39;: &#39;B&#39;}

chain_map = ChainMap(numbers, letters)
print(chain_map)
```

```
# Вывод:
ChainMap({})
ChainMap({&#39;one&#39;: 1, &#39;two&#39;: 2}, {&#39;a&#39;: &#39;A&#39;, &#39;b&#39;: &#39;B&#39;})
```

В `ChainMap` можно также передать любой из уже изученных словарей: `defaultdict, OrderedDict, Counter`. При этом нужно понимать, что поиск по `ChainMap` объекту будет учитывать особенность поиска по соответствующим словарям. Для `defaultdict`, в случае если ключ отсутствует, вернётся значение по умолчанию, для `Counter` — нулевое значение.

```py
from collections import defaultdict, OrderedDict, Counter, ChainMap

numbers = OrderedDict(one=1, two=2)
letters = defaultdict(str, {&#39;a&#39;: &#39;A&#39;, &#39;b&#39;: &#39;B&#39;})
counter = Counter(&#39;aabbbcccc&#39;)

chain_map = ChainMap(numbers, letters, counter)

print(chain_map)

# Вывод: ChainMap(OrderedDict([(&#39;one&#39;, 1), (&#39;two&#39;, 2)]), defaultdict(&lt;class &#39;str&#39;&gt;, {&#39;a&#39;: &#39;A&#39;, &#39;b&#39;: &#39;B&#39;}), Counter({&#39;c&#39;: 4, &#39;b&#39;: 3, &#39;a&#39;: 2}))
```

Либо же, можно воспользоваться методом `fromkeys()`:
```py
from collections import ChainMap

chain_map1 = ChainMap.fromkeys([&#39;one&#39;, &#39;two&#39;, &#39;three&#39;])
chain_map2 = ChainMap.fromkeys([&#39;one&#39;, &#39;two&#39;, &#39;three&#39;], -1)

print(chain_map1)
print(chain_map2)
```

```
# Вывод:
ChainMap({&#39;one&#39;: None, &#39;two&#39;: None, &#39;three&#39;: None})
ChainMap({&#39;one&#39;: -1, &#39;two&#39;: -1, &#39;three&#39;: -1})
```

Для получения значений по ключу в `ChainMap` объектах используется такой же механизм, как и в обычных словарях. Либо мы используем квадратные скобки, либо метод `get()`. Рассмотрим объект, в котором ключи повторяются:
```py
from collections import ChainMap

for_adoption = {&#39;dogs&#39;: 15, &#39;cats&#39;: 8, &#39;pythons&#39;: 9}
vet_treatment = {&#39;dogs&#39;: 7, &#39;cats&#39;: 2, &#39;tigers&#39;: 3}

pets = ChainMap(for_adoption, vet_treatment)

print(pets[&#39;dogs&#39;])
print(pets[&#39;cats&#39;])

print(pets[&#39;pythons&#39;])
print(pets[&#39;tigers&#39;])
```

```
# Вывод:
15
8
9
3
```

Как видно, в ситуации, когда у объединяемых словарей есть повторяющиеся ключи, возвращается только первое вхождение этого ключа. Таким образом, поиск по `ChainMap` объекту всегда осуществляется в том же порядке, в котором словари были указаны при создании этого объекта, при этом поиск останавливается, как только значение по нужному ключу найдено.

Встроенная функция `len()` вернёт количество уникальных ключей `ChainMap` объекта.
```py
from collections import ChainMap

for_adoption = {&#39;dogs&#39;: 15, &#39;cats&#39;: 8, &#39;pythons&#39;: 9}
vet_treatment = {&#39;dogs&#39;: 7, &#39;cats&#39;: 2, &#39;tigers&#39;: 3}

pets = ChainMap(for_adoption, vet_treatment)

print(len(pets))

# Вывод: 4
```

### Итерирование по `ChainMap`
Итерирование по `ChainMap` объекту происходит **в обратном порядке** от последнего указанного словаря к первому.
```py
from collections import ChainMap

numbers = {&#39;one&#39;: 1, &#39;two&#39;: 2}
letters = {&#39;a&#39;: &#39;A&#39;, &#39;b&#39;: &#39;B&#39;}

alpha_num = ChainMap(numbers, letters)

for key in alpha_num:
    print(key, &#39;-&gt;&#39;, alpha_num[key])
```

```
# Вывод:
a -&gt; A
b -&gt; B
one -&gt; 1
two -&gt; 2
```

При этом, если присутствуют дубликаты ключей, возвращаться будет последнее значение (имеется ввиду последнее при итерировании по объекту `ChainMap`, то есть первое значение, если идти сверху вниз).
```py
from collections import ChainMap

for_adoption = {&#39;dogs&#39;: 15, &#39;cats&#39;: 8, &#39;pythons&#39;: 9}
vet_treatment = {&#39;dogs&#39;: 7, &#39;cats&#39;: 2, &#39;tigers&#39;: 3}

pets = ChainMap(for_adoption, vet_treatment)

for key in pets:
    print(key, &#39;-&gt;&#39;, pets[key])
```

```
# Вывод:
dogs -&gt; 15
cats -&gt; 8
tigers -&gt; 3
pythons -&gt; 9
```

При итерировании, как и с обычными словарями, позволяется использовать методы `keys(), values(), items()`:
```py
from collections import ChainMap

for_adoption = {&#39;dogs&#39;: 15, &#39;cats&#39;: 8, &#39;pythons&#39;: 9}
vet_treatment = {&#39;dogs&#39;: 7, &#39;cats&#39;: 2, &#39;tigers&#39;: 3}

pets = ChainMap(for_adoption, vet_treatment)

for key in pets.keys():
    print(key, &#39;-&gt;&#39;, pets[key])

print()

for value in pets.values():
    print(value)

print()

for key, value in pets.items():
    print(key, &#39;-&gt;&#39;, value)
```

---
### Мутация `ChainMap`
Для изменения объектов типа `ChainMap` можно использовать те же способы, что и для изменения обычного словаря. Позволяется обновлять, добавлять, удалять и извлекать элементы. При этом нужно знать, что все эти операции действуют **только на первый** из объединяемых словарей.
```py
from collections import ChainMap

numbers = {&#39;one&#39;: 1, &#39;two&#39;: 2}
letters = {&#39;a&#39;: &#39;A&#39;, &#39;b&#39;: &#39;B&#39;}

alpha_num = ChainMap(numbers, letters)
print(alpha_num)

alpha_num[&#39;c&#39;] = &#39;C&#39;
print(alpha_num)

alpha_num[&#39;b&#39;] = &#39;b&#39;
print(alpha_num)

alpha_num.pop(&#39;two&#39;)
print(alpha_num)

del alpha_num[&#39;c&#39;]
print(alpha_num)

alpha_num.clear()
print(alpha_num)
```

```
# Вывод:
ChainMap({&#39;one&#39;: 1, &#39;two&#39;: 2}, {&#39;a&#39;: &#39;A&#39;, &#39;b&#39;: &#39;B&#39;})
ChainMap({&#39;one&#39;: 1, &#39;two&#39;: 2, &#39;c&#39;: &#39;C&#39;}, {&#39;a&#39;: &#39;A&#39;, &#39;b&#39;: &#39;B&#39;})
ChainMap({&#39;one&#39;: 1, &#39;two&#39;: 2, &#39;c&#39;: &#39;C&#39;, &#39;b&#39;: &#39;b&#39;}, {&#39;a&#39;: &#39;A&#39;, &#39;b&#39;: &#39;B&#39;})
ChainMap({&#39;one&#39;: 1, &#39;c&#39;: &#39;C&#39;, &#39;b&#39;: &#39;b&#39;}, {&#39;a&#39;: &#39;A&#39;, &#39;b&#39;: &#39;B&#39;})
ChainMap({&#39;one&#39;: 1, &#39;b&#39;: &#39;b&#39;}, {&#39;a&#39;: &#39;A&#39;, &#39;b&#39;: &#39;B&#39;})
ChainMap({}, {&#39;a&#39;: &#39;A&#39;, &#39;b&#39;: &#39;B&#39;})
```

{{&lt; admonition danger &gt;}}
При попытке удаления значения по ключу, которого нет **в первом словаре**, возникает ошибка `KeyError`
{{&lt; /admonition &gt;}}

Указывая в качестве первого аргумента для `ChainMap` пустой словарь, получается поведение, при котором все изменения `ChainMap` объекта не затрагивают объединяемые (исходные) словари.

---
### Сравнение `ChainMap`
Два объекта типа `ChainMap` (к примеру, `chainmap1` и `chainmap2`) считаются равными, если значение следующего выражения равно `True`: `dict(chainmap1.items()) == dict(chainmap2.items())`.

Учитывая специфику работы метода `items()`, равенство двух объектов типа `ChainMap` не гарантирует того, что эти объекты в точности совпадают:
```py
from collections import ChainMap

chainmap1 = ChainMap({&#39;a&#39;: 10, &#39;b&#39;: 20})
chainmap2 = ChainMap({&#39;a&#39;: 10, &#39;b&#39;: 20})

print(chainmap1 == chainmap2)

chainmap1 = ChainMap({&#39;a&#39;: 10, &#39;b&#39;: 20}, {&#39;a&#39;: 1, &#39;b&#39;: 2})
chainmap2 = ChainMap({&#39;a&#39;: 10, &#39;b&#39;: 20})

print(chainmap1 == chainmap2)
```

```
# Вывод:
True
True
```

---
### Атрибут `maps`
Как уже было сказано, Объект `ChainMap` хранит ссылки на все объединяемые словари во внутреннем списке, который доступен через атрибут `maps` и может быть изменён. Порядок словарей в списке `maps` соответствует порядку, в котором словари были указаны при создании объекта `ChainMap`.
```py
from collections import ChainMap

for_adoption = {&#39;dogs&#39;: 15, &#39;cats&#39;: 8, &#39;pythons&#39;: 9}
vet_treatment = {&#39;dogs&#39;: 7, &#39;cats&#39;: 2, &#39;tigers&#39;: 3}

pets = ChainMap(for_adoption, vet_treatment)

print(pets)
print(pets.maps)
print(type(pets.maps))
```

```
# Вывод:
ChainMap({&#39;dogs&#39;: 15, &#39;cats&#39;: 8, &#39;pythons&#39;: 9}, {&#39;dogs&#39;: 7, &#39;cats&#39;: 2, &#39;tigers&#39;: 3})
[{&#39;dogs&#39;: 15, &#39;cats&#39;: 8, &#39;pythons&#39;: 9}, {&#39;dogs&#39;: 7, &#39;cats&#39;: 2, &#39;tigers&#39;: 3}]
&lt;class &#39;list&#39;&gt;
```

{{&lt; admonition info &gt;}}
При создании пустого `ChainMap` объекта его `maps` будет содержать пустой словарь.
{{&lt; /admonition &gt;}}

Атрибут `maps` является обычным списком, поэтому он поддерживает все основные операции со списками. Мы можем добавлять в него новые словари, удалять уже добавленные, а также изменять их порядок.
```py
from collections import ChainMap

for_adoption = {&#39;dogs&#39;: 15, &#39;cats&#39;: 8, &#39;pythons&#39;: 9}
vet_treatment = {&#39;dogs&#39;: 7, &#39;cats&#39;: 2, &#39;tigers&#39;: 3}

pets = ChainMap(for_adoption, vet_treatment)

pets.maps.reverse()
pets.maps[0][&#39;lions&#39;] = 10
del pets.maps[1][&#39;cats&#39;]

print(pets)
print(pets.maps)
```

```
# Вывод:
ChainMap({&#39;dogs&#39;: 7, &#39;cats&#39;: 2, &#39;tigers&#39;: 3, &#39;lions&#39;: 10}, {&#39;dogs&#39;: 15, &#39;pythons&#39;: 9})
[{&#39;dogs&#39;: 7, &#39;cats&#39;: 2, &#39;tigers&#39;: 3, &#39;lions&#39;: 10}, {&#39;dogs&#39;: 15, &#39;pythons&#39;: 9}]
```

{{&lt; admonition info &gt;}}
Изменяя порядок словарей в списке атрибута `maps`, мы также меняем сами объединяемые словари, а также порядок поиска в объекте `ChainMap`
{{&lt; /admonition &gt;}}

Атрибут `maps` можно использовать для обработки абсолютно всех значений во всех словарях. С помощью этого атрибута мы можем обойти поведение по умолчанию, заключающееся в получении (изменении) первого значения из первого словаря.
```py
from collections import ChainMap

for_adoption = {&#39;dogs&#39;: 15, &#39;cats&#39;: 8, &#39;pythons&#39;: 9}
vet_treatment = {&#39;dogs&#39;: 7, &#39;cats&#39;: 2, &#39;tigers&#39;: 3}

pets = ChainMap(for_adoption, vet_treatment)

for animals in pets.maps:
    for key, value in animals.items():
        print(key, &#39;-&gt;&#39;, value)
```

```
# Вывод:
dogs -&gt; 15
cats -&gt; 8
pythons -&gt; 9
dogs -&gt; 7
cats -&gt; 2
tigers -&gt; 3
```

---
### Метод `new_child()`
`new_child()` возвращает **новый объект** `ChainMap()`, содержащий новый переданный словарь в качестве первого элемента, за которым следуют все исходные словари объекта, к которому этот метод применялся. Вызов этого метода (например, `d.new_child()`) эквивалентен вызову `ChainMap({}, *d.maps)`.
```py
from collections import ChainMap

dad = {&#39;name&#39;: &#39;Timur&#39;, &#39;age&#39;: 29}
mom = {&#39;name&#39;: &#39;Rosaly&#39;, &#39;age&#39;: 28}

old_family = ChainMap(dad, mom)

son = {&#39;name&#39;: &#39;Soslan&#39;, &#39;age&#39;: 0}

new_family = old_family.new_child(son)

print(old_family)
print(new_family)
```

```
# Вывод:
ChainMap({&#39;name&#39;: &#39;Timur&#39;, &#39;age&#39;: 29}, {&#39;name&#39;: &#39;Rosaly&#39;, &#39;age&#39;: 28})
ChainMap({&#39;name&#39;: &#39;Soslan&#39;, &#39;age&#39;: 0}, {&#39;name&#39;: &#39;Timur&#39;, &#39;age&#39;: 29}, {&#39;name&#39;: &#39;Rosaly&#39;, &#39;age&#39;: 28})
```

---
### Атрибут `parents`
`parents` возвращает **новый объект** `ChainMap`, содержащий все словари, кроме первого. Может пригодиться в случае, когда нужно пропустить первый словарь при поиске ключей. Обращение к этому атрибуту (например, `d.parents`) эквивалентно вызову `ChainMap(*d.maps[1:])`.
```py
from collections import ChainMap

dad = {&#39;name&#39;: &#39;Timur&#39;, &#39;age&#39;: 29}
mom = {&#39;name&#39;: &#39;Rosaly&#39;, &#39;age&#39;: 28}
son = {&#39;name&#39;: &#39;Soslan&#39;, &#39;age&#39;: 0}

family = ChainMap(son, dad, mom)

print(family)
print(family.parents)
print(type(family.parents))
```

```
# Вывод:
ChainMap({&#39;name&#39;: &#39;Soslan&#39;, &#39;age&#39;: 0}, {&#39;name&#39;: &#39;Timur&#39;, &#39;age&#39;: 29}, {&#39;name&#39;: &#39;Rosaly&#39;, &#39;age&#39;: 28})
ChainMap({&#39;name&#39;: &#39;Timur&#39;, &#39;age&#39;: 29}, {&#39;name&#39;: &#39;Rosaly&#39;, &#39;age&#39;: 28})
&lt;class &#39;collections.ChainMap&#39;&gt;
```

---
### Где используется
Основным вариантом использования `ChainMap` является эффективное управление несколькими областями видимости и определение приоритетов доступа дубликатов ключей. Например, в документации по `ChainMap` можно найти, как Python обращается к именами переменных в разных пространствах имён. Когда интерпретатор ищет имя (переменную), он последовательно обращается к локальной, глобальной и встроенной (`print, list, input` и т.д.) областям видимости, которые представляют из себя словари, отображающие имена на объекты.
```py
from collections import ChainMap
import builtins


input = 42

pylookup = ChainMap(locals(), globals(), vars(builtins))
print(pylookup[&#34;input&#34;])

del globals()[&#34;input&#34;]
print(pylookup[&#34;input&#34;])
```

```
# Вывод:
42
&lt;built-in function input&gt;
```

Вообще, варианты применения этого типа данных довольно специфичны:
* Эффективная группировка нескольких словарей в одном представлении
* Поиск по нескольким словарям с определенным приоритетом
* Представление цепочки значений по умолчанию и управление их приоритетами
* Повышение производительности кода, который часто вычисляет подмножества словаря

---

**Основной источник:** https://stepik.org/course/82541

Дополнительные источники:
* [Real Python](https://realpython.com/)
* [Python Docs](https://docs.python.org/3/library/collections.html)

---

> Автор: [NoisyCake](https://t.me/noisycake)  
> URL: http://localhost:1313/notes/collections2/  

