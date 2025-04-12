---
title: "Генераторы в Python"
date: 2025-04-09 00:00:01
type: posts
tags:
  - python
categories:
  - python
draft: false
---

Конспект посвящён устройству итераторов и генераторов в Python
<!--more-->

## Итераторы

### Определения
Если формально, то **итераторами** называются объекты, в которых реализованы следующие методы:
* `__iter__()`, возвращающий сам итератор;
* `__next__()`, возвращающий следующий элемент последовательности. Если элементов больше нет, возбуждается исключение `StopIteration`.

Проще говоря, итератор — объект, элементы которого можно получить <u>единожды</u>.

**Итерируемыми** называются объекты, по которым можно создать итератор (в которых есть метод `__iter__()`), поэтому таковыми в Python являются сами итераторы и все стандартные коллекции.

**Коллекция** — итерируемый объект, который содержит элементы, позволяет к ним обращаться и имеет соответствующие для себя операции.

**Последовательность** — коллекция, элементы которой проиндексированы.

### Создание и работа с итераторами
Чтобы создать итератор на основе некоторой коллекции, нужно обернуть её в функцию `iter()`. Чтобы получить следующий элемент коллекции, требуется вызвать функцию `next(iterator, default)` и положить в неё итератор.

{{< admonition info >}}
Сами по себе коллекции не являются итераторами, но на их основе можно создать сколько угодно итераторов
{{< /admonition >}}

```py
numbers = [1, 2, 3]

iterator = iter(numbers)

print(next(iterator))
print(next(iterator))
print(next(iterator))
```
```
# Вывод:
1
2
3
```

{{< admonition info >}}
По итератору можно пройтись лишь <u>единожды</u>! При этом возможно только последовательно запрашивать элементы, но не обращаться по индексу
{{< /admonition >}}

Цикл `for` также работает с итераторами. Когда в него передаётся итерируемый объект, он создаёт итератор на его основе и запрашивает элементы вплоть до получения исключения `StopIteration`. Благодаря этому, в `for` можно передать и список, и кортеж, и строку, и объект типа `range`, и многие другие объекты, которые имеют свои итераторы.

```py
numbers = [-3, 6, 1, -90, 34, -25, 23, -21]

positive_numbers = map(abs, numbers) 

for num in positive_numbers:
    print(num, end=' ')

for num in positive_numbers:
    print(num, end=' ')

# Вывод: 3 6 1 90 34 25 23 21
```
В представленном примере второй цикл ничего не выводит, поскольку итератор уже пуст.

Итераторы можно преобразовать в коллекцию, например, список:
```py
numbers = [-3, 6, 1, -90, 34, -25, 23, -21]

positive_numbers = map(abs, numbers)

positive_numbers_list1 = list(positive_numbers)
positive_numbers_list2 = list(positive_numbers)

print(positive_numbers_list1)
print(positive_numbers_list2)
```
```
# Вывод:
[3, 6, 1, 90, 34, 25, 23, 21]
[]
```

Оператор принадлежности `in` работает также и с итераторами, путём перебора всех элементов. Из этого вытекает, что итератор "опустошается" из-за работы `in`. Например:
```py
numbers = [4, 8, 15, 16, 23, 42]

iterator = iter(numbers)

print(15 in iterator)
print(15 in iterator)
```
```
# Вывод:
True
False
```

Также итератор можно распаковать, вследствие чего, опять же, он опустошится:
```py
numbers = [4, 8, 15, 16, 23, 42]

iterator = iter(numbers)

print(*iterator)
print(list(iterator))
```
```
# Вывод:
4 8 15 16 23 42
[]
```

### Ленивые вычисления
Итераторы работают лениво, то есть возвращают по одному объекту по запросу. Некоторые встроенные функции, такие как `map()`, `filter()`, `zip()`, `enumerate()`, `reversed()` и т.д. возвращают ленивые итераторы, но, например, когда итератор создаётся на основе коллекции, ленивые вычисления не работают, поскольку в памяти уже есть все элементы.

{{< admonition info >}}
Объекты, возвращаемые функцией `range()`, в отличие от объектов, возвращаемых функциями `enumerate()`, `zip()`, `reversed()`, итераторами не являются
{{< /admonition >}}

```py
sentence = 'In the face of ambiguity refuse the temptation to guess'

filter_iterator = filter(lambda word: len(word) > 4, sentence.split())
map_iterator = map(lambda word: word.upper(), filter_iterator)
enumerate_iterator = enumerate(map_iterator, 1)

for index, value in enumerate_iterator:
    print(f'{index}. {value}')
```
```
# Вывод:
1. AMBIGUITY
2. REFUSE
3. TEMPTATION
4. GUESS
```

Все три объекта `filter_iterator`, `map_iterator`, `enumerate_iterator` являются итераторами. Они не хранят все данные в памяти, а создают и выдают их по мере того, как их запрашивают. Другими словами, при обращении к очередному элементу `enumerate_iterator` произойдет последовательное обращение сначала к элементу `map_iterator`, а затем к элементу `filter_iterator`.

### Особенность `iter()`
Функция `iter()` принимает ещё один аргумент. Вот как выглядит её сигнатура: `iter(iterable, sentinel)`, где `sentinel` — стоп-значение, дойдя до которого итератор вызывает исключение `StopIteration`.

Благодаря этому добавляются сценарии использования функции, например, создать бесконечный итератор, генерирующий 0:
```py
zero_iterator = iter(int, -1)

for i in range(5):
    print(next(zero_iterator), end=' ')

print(type(zero_iterator))

# Вывод: 0 0 0 0 0
```

Другой пример: создать бесконечный генератор случайных чисел:
```py
from random import choice

def test_iter():
    values = list(range(1, 11))
    return choice(values)

random_iterator = iter(test_iter, -1)

for num in random_iterator:
    print(num)
```

Или можно читать файл, пока не найдётся пустая строка:
```py
with open('data.txt') as file:
    for line in iter(file.readline, ''):
```

---
## Генераторы
**Генератор** — функция или выражение, возвращающее итератор (объект типа `generator`), который создаёт значения по мере запроса через `yield`. Когда возвращать больше нечего, генератор вызывает исключение `StopIteration`. 

{{< admonition info >}}
Генератор является подтипом итератора, поэтому наследует все его особенности
{{< /admonition >}}

В отличии от обычных функций, генераторы используют ключевое слово `yield` вместо `return` для возврата значений. Также генераторы сохраняют свои локальные переменные от вызова к вызову.

Рассмотрим функцию-генератор, создающую последовательность целых чисел от 0 до n-1:
```py
def generate_ints(n):
    for num in range(n):
        yield num


generator1 = generate_ints(5)

print(type(generator1))

print(next(generator1))
print(next(generator1))
print(next(generator1))
print(next(generator1))
print(next(generator1))

generator2 = generate_ints(3)

for num in generator2:
    print(num)

num1, num2 = generate_ints(2)

print(num1, num2)
```
```
# Вывод:
<class 'generator'>
0
1
2
3
4
0
1
2
0 1
```

В теле генератора далеко необязательно должен быть цикл:
```py
def generate_AB():
    print('start')
    yield 'A'
    print('continue')
    yield 'B'
    print('end')

for char in generate_AB():
    print('-->', char)
```
```
# Вывод:
start
--> A
continue
--> B
end
```

В примере выше код выполняется следующим образом:
1. `for` получает итератор: `iterator = iter(generate_AB())` и на каждой итерации вызывает функцию `next(iterator)`.
2. Во время первой итерации и первом вызове `next(iterator)` генератор, перед тем как сгенерировать значение `'A'` (то есть дойти до строки `yield 'A'`), сначала выполняет строку `print('start')`.
3. Во время второй итерации генератор, перед тем как сгенерировать значение `'B'`, сначала выполняет строку `print('continue')`.
4. Во время третьей итерации генератор выполняет строку `print('end')` и завершает свою работу, возбуждая исключение `StopIteration`. Цикл `for` перехватывает это исключение и нормально завершается.

{{< admonition info >}}
Слово `return` внутри генератора будет приводить к возбуждению исключения `StopIteration` без возвращения значения
{{< /admonition >}}

### Примеры использования генераторов

#### Пример 1
Генератор `counter()`, порождающий последовательность целых чисел от значения `low` до `high` включительно с шагом один:
```py
def counter(low, high):
    for num in range(low, high + 1):
        yield num


counter1 = counter(3, 10)

for i in counter1:
    print(i)

counter2 = counter(100, 103)
print(next(counter2))
print(next(counter2))
```
```
# Вывод:
3
4
5
6
7
8
9
10
100
101
```

#### Пример 2
Генератор `even_numbers()`, порождающий бесконечную последовательность целых четных чисел от значения begin:
```py
def even_numbers(begin):
    begin += begin % 2
    while True:
        yield begin
        begin += 2


evens1 = even_numbers(10)

for index, num in enumerate(evens1):
    if index > 5:
        break
    print(num)

evens2 = even_numbers(101)

print(next(evens2))
print(next(evens2))
print(next(evens2))
print(next(evens2))
```
```
# Вывод:
10
12
14
16
18
20
102
104
106
108
```

#### Пример 3
Генератор `factorials()`, порождающий бесконечную последовательность факториалов всех натуральных чисел:
```py
def factorials():
    value = 1
    index = 1
    while True:
        yield value
        index += 1
        value *= index


infinite_factorials = factorials()

for index, num in enumerate(infinite_factorials, 1):
    if index <= 10:
        print(f'Факториал числа {index} равен {num}')
```
```
Факториал числа 1 равен 1
Факториал числа 2 равен 2
Факториал числа 3 равен 6
Факториал числа 4 равен 24
Факториал числа 5 равен 120
Факториал числа 6 равен 720
Факториал числа 7 равен 5040
Факториал числа 8 равен 40320
Факториал числа 9 равен 362880
Факториал числа 10 равен 3628800
```

### `yield from`
`yield from` — конструкция, делегирующая генерацию значений другому генератору или итерируемому объекту. По-сути она заменяет цикл `for` внутри генератора и позволяет встроить один генератор внутрь другого.

Без `yield from`:
```py
def gen1():
    yield 1
    yield 2

def gen2():
    for x in gen1():  # Ручной перебор
        yield x
    yield 3


list(gen2())  

# Вывод: [1, 2, 3]
```

С `yield from`:
```py
def gen2():
    yield from gen1()  # Делегация генерации
    yield 3


list(gen2())

# Вывод: [1, 2, 3]
```

Также `yield from` можно использовать и с другими итерируемыми объектами:
```py
def get_data():
    yield from range(5)
    yield from 'ABC'


for i in get_data():
    print(i, end=' ')


# Вывод: 0 1 2 3 4 A B C
```

Более того, конструкции `yield` и `yield from` можно использовать для написания рекурсивных генераторов. Следующий пример определяет бесконечный генератор, который порождает все целые числа со значения `start`.
```py
def numbers(start):
    if not isinstance(start, int):
        raise TypeError('Argument must be an integer')
    yield start
    yield from numbers(start + 1)


for index, number in enumerate(numbers(3)):
    if index > 5:
        break
    print(number, ' ')

# Вывод: 3 4 5 6 7 8
```

### Генераторные выражения
**Генераторное выражение** — компактная запись генератора, похожая на list comprehension, но ограниченная круглыми скобками. Его значения не хранятся в памяти, а вычисляются по мере вызова `next()`, как и в обычном генераторе.

```py
from sys import getsizeof

numbers = [1, 9, 8, 7, 90, -56, -34, 56, 100, 90, 2, 8]

even_numbers = (num for num in numbers if num % 2 == 0)         # Используем круглые скобки

print(type(even_numbers))
print(even_numbers)
print(getsizeof(even_numbers))
```
```py
<class 'generator'>
<generator object <genexpr> at 0x0000020E9C767300>
104
```

```py
squares = (i ** 2 for i in range(1, 7))         # Создаем генератор с помощью генераторного выражения
capitals = (s.upper() for s in 'abc')           # Создаем генератор с помощью генераторного выражения
stars = ('*' for i in range(5))                 # Создаем генератор с помощью генераторного выражения

for num in squares:
    print(num)

print(next(capitals))

print(*stars, end=' ')
```
```
# Вывод:
1
4
9
16
25
36
A
* * * * * 
```

{{< admonition info >}}
Генераторное выражение нельзя писать без скобок, но если оно передаётся в качестве единственного аргумента в функцию, скобки можно опустить
{{< /admonition >}}

### Конвейеры генераторов
**Конвейер генераторов** — цепочка генераторных выражений или функций, в которой каждый генератор обрабатывает данные по мере поступления от предыдущего, без загрузки всех данных в память.

```py
n = 10

integers = (i for i in range(1, n + 1))
evens = (i for i in integers if not i % 2)
squared = (i * i for i in evens)
negated = (-i for i in squared)

print(*negated)

# Вывод: -4 -16 -36 -64 -100
```

Конвейеры данных, построенные на генераторах позволяют скомпоновать код для обработки больших наборов данных без использования большого количества памяти.
```py
with open('data.csv', 'r', encoding='utf-8') as file:
    file_lines = (line for line in file)
    line_values = (line.rstrip().split(',') for line in file_lines)
    file_headers = next(line_values)
    line_dicts = (dict(zip(file_headers, data)) for data in line_values)

    result = (
        (line['user_name'], line['user_ip'])
        for line in line_dicts
        if 'stepik.org' in line['page_url']
        )

    for index, (name, ip) in enumerate(result, 1):
        print(f'{index}. {name} --- {ip}')
```

---

**Основной источник:** https://stepik.org/course/82541