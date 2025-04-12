# Функции в Python. Часть III


Третья часть функций в Python. Замыкание, декораторы и другое
&lt;!--more--&gt;

## Некоторые интересные функции
Просто некоторые функции, которыми не так часто пользуюсь и о которых могу позабыть.

1. `bin()` преобразует целое число в двоичную строку с префиксом `0b`.
    Пример:
    ```py
    print(bin(259))

    # Вывод: 0b100000011
    ```

2. `hex()` преобразует целое число в шестнадцатеричную строку с префиксом `0x`.

3. `oct()` преобразует целое число в восьмеричную строку с префиксом `0o`.

4. `ord()` возвращает число, представляющее позицию переданного символа в таблице Unicode.

5. `chr()` возвращает символ, чья позиция в таблице Unicode соответствует переданному числу.

6. `isinstance(object, classinfo)` возвращает True или False в зависимости от того, принадлежит ли `object` некоторому типу данных (`classinfo`).

7. `hash(object)` возвращает целое число, представляющее хеш-значение переданного объекта.

8. `eval(source)` выполняет строку-выражение, переданную ей в качестве обязательного аргумента, и возвращает результат выполнения этой строки.

9. `exec(source)` принимает блок кода и выполняет его, возвращая значение None.

---
## Замыкания

**Замыкание** — функция, которая &#34;помнит&#34; значения переменных из внешней области видимости, даже после того как эта область завершила выполнение. Каждый раз при выполнении внешней функции происходит создание нового экземпляра внутренней функции, с новыми ссылками на переменные внешней функции.

```py
def closure():
    count = 0
    def inner():
        nonlocal count
        count &#43;= 1
        print(count)
    return inner

start = closure()
another = closure()

start()
start()

another()

start()
```
```
# Вывод:
1
2
1
3
```

Замыкания обычно используются для:
* воздержания от жестко закодированных констант
* воздержания от использования глобальных переменных
* воздержания от создания ненужных типов данных (классов)
* реализации декораторов

{{&lt; admonition info&gt;}}
**Фабричная функция** — функция, которая создает и возвращает другую функцию (или объект), в том числе замыкания.
{{&lt; /admonition &gt;}}

### Пример 1
В этом примере функция `greeting_creator()` служит фабрикой для создания и настройки функции приветствия. Вложенная функция `greet()` может обращаться к аргументу `greeting_word` своей родительской функции `greeting_creator()`, поскольку является замыканием:
```py
def greeting_creator(greeting_word):
    def greet(name):
        return f&#39;{greeting_word}, {name}&#39;

    return greet


say_hi = greeting_creator(&#39;Hi&#39;)
say_hello = greeting_creator(&#39;Hello&#39;)

print(say_hi(&#39;Timur&#39;))
print(say_hello(&#39;Soslan&#39;))
```
```
# Вывод:
Hi, Timur
Hello, Soslan
```

### Пример 2
```py
def make_adder(n):
    return lambda x: x &#43; n

def multiplier_of(n):
    return lambda x: x * n


plus_3 = make_adder(3)
plus_5 = make_adder(5)
multiply_3 = multiplier_of(3)
multiply_5 = multiplier_of(5)

print(plus_3(10), plus_3(100))
print(plus_5(10), plus_5(100))
print(multiply_3(10), multiply_3(100))
print(multiply_5(10), multiply_5(100))
```
```
# Вывод:
13 103
15 105
30 300
50 500
```

### `nonlocal`
**Нелокальные переменные** — переменные, которые находятся внешне по отношению к текущей функции, но не являются глобальными.  
И-так, вложенная функция видит переменные в родительской функции, которые называются нелокальными или свободными.

Вложенная функция может видеть переменные внешней, но изменять их — нет. Чтобы суметь так сделать, переменную во внутренней функции нужно явно обозначить ключевым словом `nonlocal`:
```py
def outer_function():
    num = 5
    def inner_function():
        nonlocal num
        num &#43;= 10
        print(num)
    inner_function()
        
outer_function()

# Вывод: 15
```
P.S. без `nonlocal` получили бы ошибку `UnboundLocalError`.

---
## Декораторы

**Декоратор** — функция, которая принимает в качестве аргумента другую функцию, добавляет в неё дополнительный функционал и возвращает функцию с изменённым поведением.

Декораторы обычно реализуются как вложенные функции, использующие замыкание, чтобы сохранить состояние и изменить поведение оборачиваемой функции.

```py
def sample_decorator(func):
    def wrapper():
        print(&#39;Beginning&#39;)
        func()
        print(&#39;End&#39;)
    return wrapper

def say():
    print(&#39;Hello World!&#39;)

say = sample_decorator(say)

say()        
```
```
# Вывод:
Beginning
Hello World!
End
```

Здесь `sample_decorator()` является функцией-декоратором. Как можно заметить, она является функцией высшего порядка. Внутри `sample_decorator()` определена другая функция — обёртка, которая обёртывает передаваемую функцию `say()` и затем изменяет её поведение. Декоратор возвращает эту обёртку.  
Само декорирование происходит в строке `say = sample_decorator(say)`, после которого переменная `say` указывает на функцию `wrapper()`.

### Синтаксический сахар
Python позволяет использовать декораторы более простым способом с помощью символа `@`.

Приведённый пример можно переписать в виде:
```py
def sample_decorator(func):
    def wrapper():
        print(&#39;Beginning&#39;)
        func()
        print(&#39;End&#39;)
    return wrapper

@sample_decorator
def say():
    print(&#39;Hello World!&#39;)

say()        
```

Но при таком способе пропадает возможность обратиться к недекорированной функции напрямую.

### Применение нескольких декораторов
```py
def bold(func):
    def wrapper():
        return &#39;&lt;b&gt;&#39; &#43; func() &#43; &#39;&lt;/b&gt;&#39;
    return wrapper

def italic(func):
    def wrapper():
        return &#39;&lt;i&gt;&#39; &#43; func() &#43; &#39;&lt;/i&gt;&#39;
    return wrapper
```

Применим оба декоратора к функции `say()`:
```py
@bold
@italic
def greet():
    return &#39;Hello world!&#39;

print(greet())

# Вывод: &lt;b&gt;&lt;i&gt;Hello world!&lt;/i&gt;&lt;/b&gt;
```

{{&lt; admonition info &gt;}}
Декораторы применяются **снизу вверх**
{{&lt; /admonition &gt;}}

Если бы нужно было обойтись без синтаксического сахара: `greet = bold(italic(say))`

### Декорирование параметрической функции
Чтобы внутренняя функция декоратора могла обрабатывать аргументы декорируемой функции, нужно использовать `*args` и `**kwargs` в качестве параметров внутренней функции:
```py
@bold
def greet1(name):
    return f&#39;Hello {name}!&#39;

@bold
def greet2():
    return &#39;Hello world!&#39;

@bold
def greet3(name, surname):
    return f&#39;Hello {name} {surname}!&#39;

print(greet1(&#39;Ivan&#39;))
print(greet2())
print(greet3(&#39;Ivan&#39;, &#39;Ivanov&#39;))
```
```
# Вывод:
&lt;b&gt;Hello Ivan!&lt;/b&gt;
&lt;b&gt;Hello world!&lt;/b&gt;
&lt;b&gt;Hello Ivan Ivanov!&lt;/b&gt;
```

В примере выше вложенная функция `wrapper()` принимает произвольное число позиционных и именованных аргументов и передает их в декорируемую функцию `func()`. Теперь декоратор `@bold` будет работать как для функций, которые вообще не принимают аргументы, так и для функций которые принимают произвольное количество позиционных и именованных аргументов.

Пусть есть следующий декоратор:
```py
def talk(func):
    def wrapper(*args, **kwargs):
        dash = &#39;-&#39; * 15
        print(dash)
        func(*args, **kwargs)
        print(dash)

    return wrapper

@talk
def greet(name):
    return f&#39;Hello {name}!&#39;


print(greet(&#39;Ivan&#39;))
```
```
# Вывод: 
---------------
---------------
None
```

Как видно, декоратор не выдал возвращаемое функцией `func()` значение, поскольку сам ничего не возвращает. Для исправления ситуации изменим декоратор:
```py
def talk(func):
    def wrapper(*args, **kwargs):
        dash = &#39;-&#39; * 15
        result = func(*args, **kwargs)
        return dash &#43; &#39;\n&#39; &#43; result &#43; &#39;\n&#39; &#43; dash

    return wrapper

@talk
def greet(name):
    return f&#39;Hello {name}!&#39;


print(greet(&#39;Ivan&#39;))
```
```
# Вывод:
---------------
Hello Ivan!
---------------
```

{{&lt; admonition info &gt;}}
Чтобы сохранить имя функции и её строку документации при кодировании, нужно передать атрибуты декорируемой функции в обёртку. Можно сделать это вручную: `wrapper.__name__ = func.__name__` и `wrapper.__doc__ = func.__doc__`, но лучше использовать декоратор `@functools.wraps(func)` для функции-обёртки
{{&lt; /admonition &gt;}}

### Шаблоны

#### Общий шаблон
Все декораторы делают примерно одно и то же, поэтому существует шаблон декораторов:
```py
import functools

def decorator(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        # Что-то выполняется до вызова декорируемой функции
        value = func(*args, **kwargs)
        # Декорируется возвращаемое значение функции
        # или что-то выполняется после вызова декорируемой функции
        return value
    return wrapper
```

#### Измерение времени работы
Следующий декоратор измеряет и выводит время выполнения декорируемой функции. Он вычисляет время непосредственно перед запуском функции и сразу после ее завершения и выводит разницу:
```py
import functools, time

def timer(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        val = func(*args, **kwargs)
        end = time.perf_counter()
        work_time = end - start
        print(f&#39;Execution time {func.__name__}: {round(work_time, 4)} sec.&#39;)
        return val
    return wrapper

@timer
def test(n):
    return sum([(i/99)**2 for i in range(n)])

@timer
def sleep(n):
    time.sleep(n)

res1 = test(10000)
res2 = sleep(4)

print(f&#39;test func result = {res1}&#39;)
print(f&#39;sleep func result = {res2}&#39;)
```
```
Execution time test: 0.0011 sec.
Execution time sleep: 4.0006 sec.
test func result = 34005033.67003367
sleep func result = None
```

#### Отслеживание количества вызовов
Декоратор, который подсчитывает, сколько раз вызывается функция. Для сохранения состояния счетчика используется пользовательский атрибут функции:
```py
import functools

def counter(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        wrapper.num &#43;= 1
        print(f&#39;Calling {func.__name__}: {wrapper.num}&#39;)
        val = func(*args, **kwargs)
        return val
    wrapper.num = 0
    return wrapper

@counter
def greet(name):
    return f&#39;Hello {name}!&#39;

print(greet(&#39;Ivan&#39;))
print(greet(&#39;Dima&#39;))
print(greet(&#39;Arthur&#39;))
print(greet(&#39;Gvido&#39;))
```
```
# Вывод:
Calling greet: 1
Hello Ivan!
Calling greet: 2
Hello Dima!
Calling greet: 3
Hello Arthur!
Calling greet: 4
Hello Gvido!
```

### Декораторы с аргументами
Для того чтобы создать декоратор, принимающий аргументы, необходимо добавить еще один уровень вложенности, то есть создать функцию, которая возвращает нужный декоратор. Например:
```py
def print_symbols(symbol, length):
    def decorator(func):
        def wrapper(*args, **kwargs):
            print(symbol * length)
            return func(*args, **kwargs)
        return wrapper
    return decorator

@print_symbols(&#39;*&#39;, 30)
def add(a, b):
    return a &#43; b

@print_symbols(&#39;-&#39;, 10)
def mult(a, b):
    return a * b

@print_symbols(&#39;=&#39;, 40)
def diff(a, b):
    return a - b


print(add(3, 9))
print(mult(10, 20))
print(diff(100, 1))
```
```
# Вывод:
******************************
12
----------
200
========================================
99
```

{{&lt; admonition info &gt;}}
Несмотря на то что функция `print_symbols()` не является декоратором по определению, всё равно она называется декоратором с аргументами
{{&lt; /admonition &gt;}}

Немного изменим декоратор из [примера](#измерение-времени-работы). Теперь он будет измерять время выполнение программы более точно:
```py
import functools, time

def timer(iters=1):
    def decorator(func):   
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            total = 0
            for i in range(iters):
                start = time.perf_counter()
                value = func(*args, **kwargs)
                end = time.perf_counter()
                total &#43;= end - start
            print(f&#39;Average execution time {func.__name__}: {round(total/iters, 4)} sec.&#39;)
            return value
        return wrapper
    return decorator

@timer
def test(n):
    return sum([(i/99)**2 for i in range(n)])

@timer
def sleep(n):
    time.sleep(n)

res1 = test(10000)
res2 = sleep(4)

print(f&#39;test func result = {res1}&#39;)
print(f&#39;sleep func result = {res2}&#39;)
```
```
# Вывод:
Average execution time test: 0.0009 sec.
Average execution time sleep: 4.0003 sec.
test func result = 34005033.67003367
sleep func result = None
```

---

**Основной источник:** https://stepik.org/course/82541

Дополнительные источники:
* [Python Docs](https://docs.python.org/3/library/functions.html)

---

> Автор: [NoisyCake](https://t.me/noisycake)  
> URL: http://localhost:1313/notes/functions3/  

