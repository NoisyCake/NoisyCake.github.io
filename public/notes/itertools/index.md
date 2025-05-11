# Модуль itertools


Конспект посвящён некоторым функциям модуля `itertools`
&lt;!--more--&gt;

## Описание модуля
В модуле `itertools` реализованы функции для создания итераторов. Он стандартизирует базовый набор быстрых и эффективных по памяти инструментов, которые полезны сами по себе или в комбинации.

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

{{&lt; admonition info &gt;}}
Невозможно создать список на основе такого итератора, поскольку он является бесконечным
{{&lt; /admonition &gt;}}

Аргументами `start` и `step` могут быть любые числовые значения, допускающие операцию сложения.

```py
import itertools
from fractions import Fraction

frac_iter = itertools.count(1, Fraction(1, 2))
print(next(frac_iter), next(frac_iter), next(frac_iter), next(frac_iter), next(frac_iter))

# Вывод: 1 3/2 2 5/2 3
```

**Возможная реализация:**
```py
def count(start=0, step=1):
    n = start
    while True:
        yield n
        n &#43;= step
```

### `cycle()`
Функция `cycle(iterable)` создаёт итератор, циклично генерирующий последовательность элементов переданного итерируемого объекта.

```py
import itertools

for index, char in enumerate(itertools.cycle(&#39;abcd&#39;)):
    if index &lt; 7:
        print(char)
    else:
        break

cycle_iter = itertools.cycle([0, 1])
print(next(cycle_iter), next(cycle_iter), next(cycle_iter), next(cycle_iter), next(cycle_iter))

for i in zip(range(7), itertools.cycle([&#39;a&#39;, &#39;b&#39;, &#39;c&#39;])):
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
(0, &#39;a&#39;)
(1, &#39;b&#39;)
(2, &#39;c&#39;)
(3, &#39;a&#39;)
(4, &#39;b&#39;)
(5, &#39;c&#39;)
(6, &#39;a&#39;)
```

{{&lt; admonition info&gt;}}
Стоит учесть, что `cycle()` сохраняет копию каждого элемента из `iterable`, поэтому для её работы может потребоваться большой объём памяти
{{&lt; /admonition &gt;}}

**Возможная реализация:**
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

for i in itertools.repeat(&#39;bee-and-geek&#39;, 5):
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

**Возможная реализация:**
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

persons = [(&#39;Artem&#39;, &#39;Ivanov&#39;), (&#39;Arthur&#39;, &#39;Arthurov&#39;)]
pairs = [(1, 3), (2, 5), (6, 4)]
points = [(1, 1, 1), (1, 1, 2), (2, 2, 3)]

full_names = list(starmap(lambda name, surname: f&#39;{name} {surname}&#39;, persons))

print(full_names)
print(*starmap(lambda a, b: a &#43; b, pairs))
print(*starmap(lambda x, y, z: x * y * z, points))
```
```
# Вывод:
[&#39;Artem Ivanov&#39;, &#39;Arthur Arthurov&#39;]
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

**Возможная реализация:**
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
words = [&#39;is&#39;, &#39;an&#39;, &#39;of&#39;, &#39;python&#39;, &#39;C#&#39;, &#39;beegeek&#39;, &#39;is&#39;]

new_numbers = list(dropwhile(lambda num: num &lt;= 5, numbers))
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

**Возможная реализация:**
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
words = [&#39;is&#39;, &#39;an&#39;, &#39;of&#39;, &#39;python&#39;, &#39;C#&#39;, &#39;is&#39;]

new_numbers = list(takewhile(lambda num: num &lt;= 5, numbers))
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

**Возможная реализация:**
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
words = [&#39;is&#39;, &#39;an&#39;, &#39;of&#39;, &#39;python&#39;, &#39;C#&#39;, &#39;is&#39;]

new_numbers = list(filterfalse(lambda num: num &lt;= 5, numbers))
print(new_numbers)

for word in filterfalse(lambda s: len(s) == 2, words):
    print(word)
```
```
# Вывод:
[6, 7, 8, 9, 10]
python
```

{{&lt; admonition info &gt;}}
Если `predicate=None`, то фильтрующая функция равнозначна функции `bool()`
{{&lt; /admonition &gt;}}

**Возможная реализация:**
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

data = &#39;ABCDEF&#39;
selectors = [True, False, True, False, True, False]

result = compress(data, selectors)
print(list(result))

# Вывод: [&#39;A&#39;, &#39;C&#39;, &#39;E&#39;]
```

**Возможная реализация:**
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
Функция `chain(*iterables)` создаёт итератор, последовательно возвращающий элементы всех переданных итерируемых объектов.

```py
from itertools import chain

chain_iter1 = chain(&#39;ABC&#39;, &#39;DEF&#39;)
print(*chain_iter1)

chain_iter2 = chain(enumerate(&#39;ABC&#39;))
print(*chain_iter2)

for i in chain([1, 2, 3], [&#39;a&#39;, &#39;b&#39;, &#39;c&#39;], (&#39;Shmidt&#39;, 20, &#39;Male&#39;, &#39;Student&#39;)):
    print(i, end=&#39; &#39;)
```
```
# Вывод:
A B C D E F
(0, &#39;A&#39;) (1, &#39;B&#39;) (2, &#39;C&#39;)
1 2 3 a b c Shmidt 20 Male Student
```

**Возможная реализация:**
```py
def chain(*iterables):
    for it in iterables:
        for element in it:
            yield element
```

### `chain.from_iterable()`
Функция `chain.from_iterable(iterable)` создаёт итератор, возвращающий все элементы итерируемого объекта, включая вложенные элементы вложенных.

{{&lt; admonition info &gt;}}
Все вложенные объекты должны быть итерируемыми
{{&lt; /admonition &gt;}}

```py
from itertools import chain

chain_iter1 = chain.from_iterable([&#39;ABC&#39;, &#39;DEF&#39;])      # передаем список
print(*chain_iter1)

chain_iter2 = chain.from_iterable(enumerate(&#39;ABC&#39;))
print(*chain_iter2)

for i in chain.from_iterable([&#39;Shmidt&#39;, &#39;20&#39;, &#39;Male&#39;, &#39;Student&#39;]):
    print(i, end=&#39; &#39;)
```
```
# Вывод:
A B C D E F
0 A 1 B 2 C
S h m i d t 2 0 M a l e S t u d e n t
```

**Возможная реализация:**
```py
def from_iterable(iterables):
    for it in iterables:
        for element in it:
            yield element
```

### `zip_longest()`
Функция `zip_longest(*iterables, fillvalue=None)` работает аналогично `zip()`, за тем исключением, что работает до исчерпания всех переданных итерируемых объектов.

```py
from itertools import zip_longest

print(*zip([1, 2, 3], [&#39;a&#39;, &#39;b&#39;, &#39;c&#39;, &#39;d&#39;, &#39;e&#39;]))
print(*zip_longest([1, 2, 3], [&#39;a&#39;, &#39;b&#39;, &#39;c&#39;, &#39;d&#39;, &#39;e&#39;]))                     # fillvalue=None
print(*zip_longest([1, 2, 3], [&#39;a&#39;, &#39;b&#39;, &#39;c&#39;, &#39;d&#39;, &#39;e&#39;], fillvalue=&#39;*&#39;))
print(*zip_longest([&#39;a&#39;, &#39;b&#39;, &#39;c&#39;, &#39;d&#39;, &#39;e&#39;], [1, 2, 3], fillvalue=777))
```
```
(1, &#39;a&#39;) (2, &#39;b&#39;) (3, &#39;c&#39;)
(1, &#39;a&#39;) (2, &#39;b&#39;) (3, &#39;c&#39;) (None, &#39;d&#39;) (None, &#39;e&#39;)
(1, &#39;a&#39;) (2, &#39;b&#39;) (3, &#39;c&#39;) (&#39;*&#39;, &#39;d&#39;) (&#39;*&#39;, &#39;e&#39;)
(&#39;a&#39;, 1) (&#39;b&#39;, 2) (&#39;c&#39;, 3) (&#39;d&#39;, 777) (&#39;e&#39;, 777)
```

### `tee()`
Функция `tee(iterable, n=2)` создаёт `n` независимых итераторов на основе одного итерируемого объекта.

```py
from itertools import tee

iter1, iter2 = tee([1, &#39;a&#39;, 2, &#39;b&#39;, 3, &#39;c&#39;])    # по умолчанию n=2

print(*iter1)
print(*iter2)
```
```
# Вывод:
1 a 2 b 3 c
1 a 2 b 3 c
```

Новые итераторы, созданные функцией `tee()`, разделяют данные c исходным итерируемым объектом, и поэтому после их создания исходный итерируемый объект не должен изменяться.

```py
from itertools import tee

data = [1, 2, 3, 4, 5]

iter1, iter2 = tee(data)

data.append(6)                      

print(*iter1)
print(*iter2)
```
```
# Вывод:
1 2 3 4 5 6
1 2 3 4 5 6
```

### `pairwise()`
Функция `pairwise(iterable)` создаёт итератор, возвращающий последовательные перекрывающие пары в виде кортежей.

```py
from itertools import pairwise

print(*pairwise(&#39;ABCDEFG&#39;))
print(*pairwise([1, 2, 3, 4, 5]))
```
```
# Вывод:
(&#39;A&#39;, &#39;B&#39;) (&#39;B&#39;, &#39;C&#39;) (&#39;C&#39;, &#39;D&#39;) (&#39;D&#39;, &#39;E&#39;) (&#39;E&#39;, &#39;F&#39;) (&#39;F&#39;, &#39;G&#39;)
(1, 2) (2, 3) (3, 4) (4, 5)
```

**Возможная реализация:**
```py
def pairwise(iterable):
    a, b = tee(iterable)
    next(b, None)
    return zip(a, b)
```

---
## `groupby()`

Функция `groupby(iterable, key=None)` используется для группировки смежных элементов итерируемого объекта. Она возвращает итератор, содержащий кортежи, каждый из которых состоит из двух элементов: первый — значение, характеризующее группу, второй — итератор, содержащий элементы соответствующей группы.  
`key` — функция, характеризующая группу. Если не указана, используется функция тождественности.

```py
from itertools import groupby

numbers = [1, 1, 1, 7, 7, 7, 7, 15, 7, 7, 7]

group_iter = groupby(numbers)

for key, values in group_iter:
    print(f&#39;{key}: {list(values)}&#39;)  
```
```
# Вывод:
1: [1, 1, 1]
7: [7, 7, 7, 7]
15: [15]
7: [7, 7, 7]
```

Посмотрим, как работает `key`:
```py
from itertools import groupby

numbers = [1, 1, 1, 7, 7, 7, 7, 15, 7, 7, 7]

group_iter = groupby(numbers, key=lambda num: num &lt; 10)

for key, values in group_iter:
    print(f&#39;{key}: {list(values)}&#39;)
```
```
# Вывод:
True: [1, 1, 1, 7, 7, 7, 7]
False: [15]
True: [7, 7, 7]
```

Взглянем на примеры использования `groupby()` в реальных задачах.

### Пример 1
Задача: удалить подряд идущие одинаковые элементы в списке:
```py
from itertools import groupby

data = [&#39;A&#39;, &#39;A&#39;, &#39;A&#39;, &#39;A&#39;, &#39;B&#39;, &#39;B&#39;, &#39;B&#39;, &#39;C&#39;, &#39;C&#39;, &#39;D&#39;, &#39;A&#39;, &#39;A&#39;, &#39;B&#39;, &#39;B&#39;, &#39;B&#39;]

result = [key for key, group in groupby(data)] 

print(result)

# Вывод: [&#39;A&#39;, &#39;B&#39;, &#39;C&#39;, &#39;D&#39;, &#39;A&#39;, &#39;B&#39;]
```

### Пример 2
Задача: получить список с уникальными элементами списка:
```py
from itertools import groupby

data = [&#39;A&#39;, &#39;A&#39;, &#39;A&#39;, &#39;A&#39;, &#39;B&#39;, &#39;B&#39;, &#39;B&#39;, &#39;C&#39;, &#39;C&#39;, &#39;D&#39;, &#39;A&#39;, &#39;A&#39;, &#39;B&#39;, &#39;B&#39;, &#39;B&#39;]

result = [key for key, group in groupby(sorted(data))] 

print(result)

# Вывод: [&#39;A&#39;, &#39;B&#39;, &#39;C&#39;, &#39;D&#39;]
```

### Пример 3
Задача: определить, какой символ встречается чаще всего в строке:
```py
from itertools import groupby

data = &#39;aaabcdaabcccdddcccccccbrttbcc&#39;
group_iter = groupby(sorted(data))

max_result = max(group_iter, key=lambda tpl: sum(1 for i in tpl[1]))

print(&#39;Символ встречающийся чаще всего в строке:&#39;, max_result[0])

# Вывод: Символ встречающийся чаще всего в строке: c
```

---
## Комбинаторные функции

### `permutations()`
Функция `permutations(iterable, r=None)` создаёт итератор, возвращающий все перестановки элементов `iterable` в виде кортежей. Для задания длины кортежей используется `r`.

```py
from itertools import permutations

numbers = [1, 2, 3, 4]
letters = &#39;cba&#39;

all_num_permutations = permutations(numbers)
all_let_permutations = permutations(letters)

print(list(all_num_permutations))
print(list(all_let_permutations))
```
```
# Вывод:
[(1, 2, 3, 4), (1, 2, 4, 3), (1, 3, 2, 4), (1, 3, 4, 2), (1, 4, 2, 3), (1, 4, 3, 2), (2, 1, 3, 4), (2, 1, 4, 3), (2, 3, 1, 4), (2, 3, 4, 1), (2, 4, 1, 3), (2, 4, 3, 1), (3, 1, 2, 4), (3, 1, 4, 2), (3, 2, 1, 4), (3, 2, 4, 1), (3, 4, 1, 2), (3, 4, 2, 1), (4, 1, 2, 3), (4, 1, 3, 2), (4, 2, 1, 3), (4, 2, 3, 1), (4, 3, 1, 2), (4, 3, 2, 1)]
[(&#39;c&#39;, &#39;b&#39;, &#39;a&#39;), (&#39;c&#39;, &#39;a&#39;, &#39;b&#39;), (&#39;b&#39;, &#39;c&#39;, &#39;a&#39;), (&#39;b&#39;, &#39;a&#39;, &#39;c&#39;), (&#39;a&#39;, &#39;c&#39;, &#39;b&#39;), (&#39;a&#39;, &#39;b&#39;, &#39;c&#39;)]
```

Если передать `r`, получим все размещения из `n` элементов по `r`, где `n` — длина переданного итерируемого объекта.
```py
from itertools import permutations

letters = [&#39;a&#39;, &#39;b&#39;, &#39;c&#39;]

permutations1 = permutations(letters, r=1)
permutations2 = permutations(letters, r=2)
permutations3 = permutations(letters, r=3)

print(list(permutations1))
print(list(permutations2))
print(list(permutations3))
```
```
# Вывод:
[(&#39;a&#39;,), (&#39;b&#39;,), (&#39;c&#39;,)]
[(&#39;a&#39;, &#39;b&#39;), (&#39;a&#39;, &#39;c&#39;), (&#39;b&#39;, &#39;a&#39;), (&#39;b&#39;, &#39;c&#39;), (&#39;c&#39;, &#39;a&#39;), (&#39;c&#39;, &#39;b&#39;)]
[(&#39;a&#39;, &#39;b&#39;, &#39;c&#39;), (&#39;a&#39;, &#39;c&#39;, &#39;b&#39;), (&#39;b&#39;, &#39;a&#39;, &#39;c&#39;), (&#39;b&#39;, &#39;c&#39;, &#39;a&#39;), (&#39;c&#39;, &#39;a&#39;, &#39;b&#39;), (&#39;c&#39;, &#39;b&#39;, &#39;a&#39;)]
```

{{&lt; admonition info &gt;}}
Элементы итерируемого объекта рассматриваются как уникальные в зависимости от их положения, а не от их значения. Поэтому, если элементы уникальны, повторных значений в каждой перестановке не будет, иначе будут повторы
{{&lt; /admonition &gt;}}

### `combinations()`
Функция `combinations(iterable, r)` создаёт итератор, возвращающий сочетания из `n` по `r` элементов `iterable`.

```py
from itertools import combinations

numbers = [1, 2, 3, 4]

print(list(combinations(numbers, r=1)))
print(list(combinations(numbers, r=2)))
print(list(combinations(numbers, r=3)))
print(list(combinations(numbers, r=4)))
```
```
# Вывод:
[(1,), (2,), (3,), (4,)]
[(1, 2), (1, 3), (1, 4), (2, 3), (2, 4), (3, 4)]
[(1, 2, 3), (1, 2, 4), (1, 3, 4), (2, 3, 4)]
[(1, 2, 3, 4)]
```

{{&lt; admonition info &gt;}}
Аналогично предыдущей функции, отсутствие повторений гарантируется только если элементы `iterable` уникальны
{{&lt; /admonition &gt;}}

### `combinations_with_replacement()`
Функция `combinations_with_replacement(iterable, r)` создаёт итератор, возвращающий сочетания с повторами из `n` по `r` элементов `iterable`.

```py
from itertools import combinations_with_replacement

numbers = [1, 2, 3, 4]

print(list(combinations_with_replacement(numbers, 1)))
print(list(combinations_with_replacement(numbers, 2)))
print(list(combinations_with_replacement(numbers, 3)))
```
```
# Вывод:
[(1,), (2,), (3,), (4,)]
[(1, 1), (1, 2), (1, 3), (1, 4), (2, 2), (2, 3), (2, 4), (3, 3), (3, 4), (4, 4)]
[(1, 1, 1), (1, 1, 2), (1, 1, 3), (1, 1, 4), (1, 2, 2), (1, 2, 3), (1, 2, 4), (1, 3, 3), (1, 3, 4), (1, 4, 4), (2, 2, 2), (2, 2, 3), (2, 2, 4), (2, 3, 3), (2, 3, 4), (2, 4, 4), (3, 3, 3), (3, 3, 4), (3, 4, 4), (4, 4, 4)]
```

### `product()`
Функция `product(*iterables, repeat=1)` создаёт итератор, возвращающий декартово произведение всех переданных итерируемых объектов.

{{&lt; admonition info &gt;}}
Декартовым произведением A и B называется множество, содержащее все упорядоченные пары (a, b)
{{&lt; /admonition &gt;}}

```py
from itertools import product

numbers = [1, 2]
letters = [&#39;x&#39;, &#39;y&#39;, &#39;z&#39;]
flags = [False, True]

print(list(product(numbers, letters)))
print(list(product(letters, numbers)))
print(list(product(letters, numbers, flags)))
```
```
# Вывод:
[(1, &#39;x&#39;), (1, &#39;y&#39;), (1, &#39;z&#39;), (2, &#39;x&#39;), (2, &#39;y&#39;), (2, &#39;z&#39;)]
[(&#39;x&#39;, 1), (&#39;x&#39;, 2), (&#39;y&#39;, 1), (&#39;y&#39;, 2), (&#39;z&#39;, 1), (&#39;z&#39;, 2)]
[(&#39;x&#39;, 1, False), (&#39;x&#39;, 1, True), (&#39;x&#39;, 2, False), (&#39;x&#39;, 2, True), (&#39;y&#39;, 1, False), (&#39;y&#39;, 1, True), (&#39;y&#39;, 2, False), (&#39;y&#39;, 2, True), (&#39;z&#39;, 1, False), (&#39;z&#39;, 1, True), (&#39;z&#39;, 2, False), (&#39;z&#39;, 2, True)]
```

Чтобы вычислить декартово произведение итерируемого объекта с самим собой, можно использовать необязательный аргумент `repeat`:
```py
from itertools import product

letters = &#39;abc&#39;

print(list(product(letters, repeat=2)))

# Вывод: [(&#39;a&#39;, &#39;a&#39;), (&#39;a&#39;, &#39;b&#39;), (&#39;a&#39;, &#39;c&#39;), (&#39;b&#39;, &#39;a&#39;), (&#39;b&#39;, &#39;b&#39;), (&#39;b&#39;, &#39;c&#39;), (&#39;c&#39;, &#39;a&#39;), (&#39;c&#39;, &#39;b&#39;), (&#39;c&#39;, &#39;c&#39;)]
```

---

**Основной источник:** https://stepik.org/course/82541

---

> Автор: [NoisyCake](https://t.me/noisycake)  
> URL: http://localhost:1313/notes/itertools/  

