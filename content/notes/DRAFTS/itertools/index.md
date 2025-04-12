---
title: "Модуль itertools"
date: 2025-04-10 00:00:01
type: posts
tags:
  - python
  - modules
  - itertools
categories:
  - python
draft: true
---

Конспект посвящён некоторым функциям модуля `itertools`
<!--more-->

## Описание модуля
В модуле `itertools` реализованы функции для создания итераторов. Он стандартизирует базовый набор быстрых и экономичных с точки зрения памяти инструментов, которые полезны сами по себе или в комбинации

Прообразом генераторных функций модуля `itertools` послужили аналогичные функции из таких языков функционального программирования, как Clojure, Haskell, APL и SML.

Функции данного модуля можно разделить на следующие категории:

* Порождающие данные;
* Фильтрующие данные;
* Преобразующие данные;
* Группирующие данные;
* Объединяющие или разделяющие данные;
* Порождающие комбинаторные данные.

---
## Функции, порождающие данные

Функции `count()`, `cycle()` и `repeat()` по умолчанию порождают бесконечные итераторы.

### `count()`
Функция `count(start=0, step=1)` создаёт итератор, который возвращает бесконечную последовательность чисел, начиная со `start`.

```py
import itertools

count1 = itertools.count()

print(next(count1), next(count1), next(count1))

# Вывод: 0 1 2
```

{{< admonition info >}}
Невозможно создать список на основе такого итератора, поскольку он является бесконечным
{{< /admonition >}}

Аргументами `start` и `step` могут быть любые числовые значения, допускающие операцию сложения.

```py
import itertools
from fractions import Fraction

frac_iter = count(1, Fraction(1, 2))
print(next(frac_iter), next(frac_iter), next(frac_iter), next(frac_iter), next(frac_iter))

# Вывод: 1 3/2 2 5/2 3
```

**Возможная реализация:**
```py
def count(start=0, step=1):
    n = start
    while True:
        yield n
        n += step
```

### `cycle()`
Функция `cycle(iterable)` создаёт итератор, циклично генерирующий последовательность элементов переданного итерируемого объекта.

```py
import itertools

for index, char in enumerate(itertools.cycle('abcd')):
    if index < 7:
        print(char)
    else:
        break

cycle_iter = itertools.cycle([0, 1])
print(next(cycle_iter), next(cycle_iter), next(cycle_iter), next(cycle_iter), next(cycle_iter))

for i in zip(range(7), itertools.cycle(['a', 'b', 'c'])):
    print(i)
```
```
# Вывод:
a
b
c
d
a
b
c
0 1 0 1 0
(0, 'a')
(1, 'b')
(2, 'c')
(3, 'a')
(4, 'b')
(5, 'c')
(6, 'a')
```

{{< admonition info>}}
Стоит учесть, что `cycle()` сохраняет копию каждого элемента из `iterable`, поэтому для её работы может потребоваться большой объём памяти
{{< /admonition >}}

**Возможная реализация**
```py
def cycle(iterable):
    saved = []
    for element in iterable:
        yield element
        saved.append(element)
    while saved:
        for element in saved:
            yield element
```

### `repeat()`
Функция `repeat(object[, times])` создаёт итератор, который возвращает объект снова и снова. Количество генераций можно ограничить аргументов `times`.

```py
import itertools

for i in itertools.repeat('bee-and-geek', 5):
    print(i)

repeat_iter = itertools.repeat([1, 2, 3])

print(next(repeat_iter))
print(next(repeat_iter))
print(next(repeat_iter))
```
```
# Вывод:
bee-and-geek
bee-and-geek
bee-and-geek
bee-and-geek
bee-and-geek
[1, 2, 3]
[1, 2, 3]
[1, 2, 3]
```

**Возможная реализация**
```py
def repeat(object, times=None):
    if times is None:
        while True:
            yield object
    else:
        for i in range(times):
            yield object
```

### `starmap()`
Функция `starmap(function, iterable)` создаёт итератор, элементами которого являются элементы `iterable`, к которым была применена функция `func`. Она используется вместо `map()`, когда элементами итерируемого объекта являются другие итерируемые объекты.

```py
from itertools import starmap

persons = [('Artem', 'Ivanov'), ('Arthur', 'Arthurov')]
pairs = [(1, 3), (2, 5), (6, 4)]
points = [(1, 1, 1), (1, 1, 2), (2, 2, 3)]

full_names = list(starmap(lambda name, surname: f'{name} {surname}', persons))

print(full_names)
print(*starmap(lambda a, b: a + b, pairs))
print(*starmap(lambda x, y, z: x * y * z, points))
```
```
# Вывод:
['Artem Ivanov', 'Arthur Arthurov']
4 7 10
1 2 12
```

### `accumulate()`
Функция `accumulate(iterable[, function, *, initial=None])` создаёт итератор, возвращающий накопленные суммы или накопленные результаты `func`. Она работает аналогично функции `reduce()` за тем исключением, что `accumulate()` также генерирует промежуточные результаты.

```py
from itertools import accumulate
import operator

data = [3, 4, 6, 2, 1, 9, 0, 7, 5, 8]

print(list(accumulate(data)))
print(list(accumulate(data, operator.mul)))
print(list(accumulate(data, max)))
print(list(accumulate(data, min)))
```
```
# Вывод:
[3, 7, 13, 15, 16, 25, 25, 32, 37, 45]
[3, 12, 72, 144, 144, 1296, 0, 0, 0, 0]
[3, 4, 6, 6, 6, 9, 9, 9, 9, 9]
[3, 3, 3, 2, 1, 1, 0, 0, 0, 0]
```

Обычно количество элементов результирующего итератора совпадает с количеством элементов итерируемого объекта. Однако, если задано значение аргумента `initial`, то накопление начинается с начального значения `initial`, и в этом случае результирующий итератор будет иметь один дополнительный элемент.

```py
from itertools import accumulate

print(list(accumulate([1, 2, 3, 4, 5], initial=100)))

# Вывод: [100, 101, 103, 106, 110, 115]
```

**Возможная реализация**
```py
import operator

def accumulate(iterable, func=operator.add, *, initial=None):
    it = iter(iterable)
    total = initial
    if initial is None:
        try:
            total = next(it)
        except StopIteration:
            return
    yield total
    for element in it:
        total = func(total, element)
        yield total
```

---
## Функции, фильтрующие данные

### `dropwhile()`
Функция `dropwhile(predicate, iterable)` создаёт итератор, возвращающий элементы `iterable`, начиная с элемента, для которого `predicate` вернул `False`.

```py
from itertools import dropwhile

numbers = [1, 1, 2, 3, 4, 4, 5, 6, 7, 8, 9, 10, 1, 2, 3]
words = ['is', 'an', 'of', 'python', 'C#', 'beegeek', 'is']

new_numbers = list(dropwhile(lambda num: num <= 5, numbers))
print(new_numbers)

for word in dropwhile(lambda s: len(s) == 2, words):
    print(word)
```
```
# Вывод:
[6, 7, 8, 9, 10, 1, 2, 3]
python
C#
beegeek
is
```

**Возможная реализация**
```py
def dropwhile(predicate, iterable):
    iterable = iter(iterable)
    for x in iterable:
        if not predicate(x):
            yield x
            break
    for x in iterable:
        yield x
```

### `takewhile()`
Функция `takewhile(predicate, iterable)` создаёт итератор, возвращающий элементы `iterable` до тех пор, пока для элемента `predicate` не вернёт `False`.

```py
from itertools import takewhile

numbers = [1, 1, 2, 3, 4, 4, 5, 6, 7, 8, 9, 10, 1, 2, 3]
words = ['is', 'an', 'of', 'python', 'C#', 'beegeek', 'is']

new_numbers = list(takewhile(lambda num: num <= 5, numbers))
print(new_numbers)

for word in takewhile(lambda s: len(s) == 2, words):
    print(word)
```
```
# Вывод:
[1, 1, 2, 3, 4, 4, 5]
is
an
of
```

**Возможная реализация**
```py
def takewhile(predicate, iterable):
    for x in iterable:
        if predicate(x):
            yield x
        else:
            break
```

### `filterfalse()`
Функция `filterfalse(predicate, iterable)` создаёт итератор, возвращающий только те элементы `iterable`, для которых `predicate` вернул `False`. По-сути, она противоположна функции `filter()`.

```py
from itertools import filterfalse

numbers = [1, 1, 2, 3, 4, 4, 5, 6, 7, 8, 9, 10, 1, 2, 3]
words = ['is', 'an', 'of', 'python', 'C#', 'beegeek', 'is']

new_numbers = list(filterfalse(lambda num: num <= 5, numbers))
print(new_numbers)

for word in filterfalse(lambda s: len(s) == 2, words):
    print(word)
```
```
# Вывод:
[6, 7, 8, 9, 10]
python
beegeek
```

{{< admonition info >}}
Если `predicate=None`, то фильтрующая функция равнозначна функции `bool()`
{{< /admonition >}}

**Возможная реализация**
```py
def filterfalse(predicate, iterable):
    if predicate is None:
        predicate = bool
    for x in iterable:
        if not predicate(x):
            yield x
```

### `compress()`
Функция `compress(data, selectors)` создаёт итератор, возвращающий элементы `iterable` в соответствии с маской `selectors`. Она остановится, когда любой из переданных объектов закончится.

```py
from itertools import compress

data = 'ABCDEF'
selectors = [True, False, True, False, True, False]

result = compress(data, selectors)
print(list(result))

# Вывод: ['A', 'C', 'E']
```

**Возможная реализация**
```py
def compress(iterable, selectors):
    for value, selector in zip(iterable, selectors):
        if selector:
            yield value
```

### `islice()`
Функция `islice(iterable, start, stop[, step])` создаёт итератор, возвращающий срез `iterable` от `start` до `stop` с шагом `step`. Работает как обычные срезы, за тем исключением, что отрицательные значения индексов и шага не поддерживаются.

```py
from itertools import islice

print(*islice(range(10), None))
print(*islice(range(100), 5))
print(*islice(range(100), 5, 10))
print(*islice(range(100), 0, 100, 10))
```
```
# Вывод:
0 1 2 3 4 5 6 7 8 9
0 1 2 3 4
5 6 7 8 9
0 10 20 30 40 50 60 70 80 90
```

---
## Функции, объединяющие и разделяющие данные

### `chain()`
Функция `chain()` 
































---

**Основной источник:** https://stepik.org/course/82541