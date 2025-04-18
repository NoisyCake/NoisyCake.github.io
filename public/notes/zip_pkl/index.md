# Работа с zip и pkl файлами в Python


Управление файлами формата `zip, pkl` в языке программирования Python
&lt;!--more--&gt;
## Формат zip
`zip` — это формат сжатия без потерь: после распаковки данные будут такими же, как перед сжатием. Алгоритм ищет избыточности в исходных данных и эффективнее представляет информацию.

Формат zip обладает следующими преимуществами:
- является полностью открытым
- является одним из самых популярных (большинство архивов в Internet – это архивы zip)
- является очень быстрым

{{&lt; admonition info &gt;}}
Формат сжатия без потерь отличается от сжатия с потерями, который используется в таких форматах, как JPEG и MP3: при сжатии выбрасывается часть информации, которая менее заметна для человеческого глаза или уха.
{{&lt; /admonition &gt;}}

Сначала разберёмся, как считать степень сжатия файла — коэффициент *K*, определяемый как отношение объема сжатого файла к объему исходного файла, выраженным в процентах:

$$
 K = \frac{V_{c}} {V_{o}} * 100\\%
$$

Степень сжатия зависит от используемой программы, метода сжатия и типа исходного файла. Наиболее хорошо сжимаются файлы графических образов, текстовые файлы и файлы данных, для которых степень сжатия может достигать 5–40%, меньше сжимаются файлы исполняемых программ и загрузочных модулей — 60–90%. Почти не сжимаются архивные файлы.

---
## Модуль `zipfile`
В этом конспекте будет рассмотрен объекты `ZipFile` и `ZipInfo`, однако модуль `zipfile` содержит и другие классы.
Для использования: `from zipfile import ZipFile`

### Методы `ZipFile`
* `printdir()` — выводит таблицу с информацией о содержимом архива: полные названия файлов с указанием даты изменения и размера в байтах.
```py
from zipfile import ZipFile

with ZipFile(&#39;test.zip&#39;) as zip_file:
    zip_file.printdir()
```

```
# Вывод:
File Name                                             Modified             Size
test/                                          2021-11-27 12:47:10            0
test/Картинки/                                 2021-11-27 12:49:02            0
test/Картинки/1.jpg                            2021-09-02 12:30:20        90156
test/Картинки/avatar.png                       2021-08-20 09:38:44        19053
test/Картинки/certificate.png                  2021-10-23 09:46:36        43699
test/Картинки/py.png                           2021-07-28 17:55:56        33522
test/Картинки/World_Time_Zones_Map.png         2021-11-08 07:30:06      2324421
test/Картинки/Снимок экрана.png                2021-10-01 20:47:02        10878
test/Неравенства.djvu                          2021-08-19 08:39:06      5283010
test/Программы/                                2021-11-27 12:48:20            0
test/Программы/image_util.py                   2021-11-18 12:42:22         4955
test/Программы/sort.py                         2021-11-14 19:31:02           61
test/Разные файлы/                             2021-11-27 12:48:10            0
test/Разные файлы/astros.json                  2021-11-08 09:29:58          505
```

{{&lt; admonition info &gt;}}
При создании объекта `ZipFile` мы также можем передать необязательный аргумент `mode`, который задает режим работы (по аналогии с обычными файлами): `r` — чтение, `w` — запись в новый файл, `a` — запись в конец существующего файла
{{&lt; /admonition &gt;}}

* `infolist()` — позволяет получить информацию о файлах из архива в виде списка объектов `ZipInfo`, которые содержат дополнительную информацию о каждом файле: `file_size`, `compress_size`, `filename`, `date_time` и другие.
```py
from zipfile import ZipFile

with ZipFile(&#39;test.zip&#39;) as zip_file:
    info = zip_file.infolist()
    print(info[6].file_size)              # размер начального файла в байтах
    print(info[6].compress_size)          # размер сжатого файла в байтах
    print(info[6].filename)               # имя файла
    print(info[6].date_time)              # дата изменения файла
```

```
# Вывод:
2324421
2322032
test/Картинки/World_Time_Zones_Map.png
(2021, 11, 8, 7, 30, 6)
```

Метод `is_dir()` объекта `ZipInfo` нужен, чтобы проверить, является ли этот объект файлом (возвращает `False`) или папкой (`True`).

* `namelist()` — возвращает список названий файлов и директорий, содержащихся в архиве.
```py
from zipfile import ZipFile

with ZipFile(&#39;test.zip&#39;) as zip_file:
    info = zip_file.namelist()
    print(*info, sep=&#39;\n&#39;)
```

```
# Вывод:
test/
test/Картинки/
test/Картинки/1.jpg
test/Картинки/avatar.png
test/Картинки/certificate.png
test/Картинки/py.png
test/Картинки/World_Time_Zones_Map.png
test/Картинки/Снимок экрана.png
test/Неравенства.djvu
test/Программы/
test/Программы/image_util.py
test/Программы/sort.py
test/Разные файлы/
test/Разные файлы/astros.json
```

* `getinfo()` — в отличие от метода `infolist()`, который позволяет получить информацию о всех файлах из архива в виде списка специальных объектов (тип `ZipInfo`), метод `getinfo()` позволяет получить информацию о конкретном файле по его имени в архиве.

```py
from zipfile import ZipFile

with ZipFile(&#39;test.zip&#39;) as zip_file:
    info = zip_file.namelist()                # получаем названия всех файлов архива
    last_file = zip_file.getinfo(info[-4])    # получаем информацию об отдельном файле
    print(last_file.file_size)
    print(last_file.compress_size)
    print(last_file.filename)
    print(last_file.date_time)
```

```
4955
1641
test/Программы/image_util.py
(2021, 11, 18, 12, 42, 22)
```

При создании объекта `ZipFile` мы также можем передать еще два необязательных аргумента:
* `compression`, который определяет метод сжатия, который должен использоваться при записи в архив. Он принимает одно из значений: `ZIP_STORED, ZIP_DEFLATED, ZIP_BZIP2, ZIP_LZMA`. По умолчанию используется значение `compression=ZIP_STORED`
* `allowZip64`, который позволяет разрешить использование расширений `zip64`, которые дают возможность создавать архивы размером больше 4 гигабайт. По умолчанию равен `allowZip64=True`

Для того чтобы проверить является ли некоторый файл `zip` архивом, используется функция `zipfile.is_zipfile()`, которая принимает на вход путь к файлу (или сам файловый объект) и возвращает значение `True`, если указанный файл является `zip` архивом, или `False` в противном случае.

---
### Работы с конкретными файлами архива
Откроем отдельный файла из архива:
```py
from zipfile import ZipFile

with ZipFile(&#39;test.zip&#39;) as zip_file:
    with zip_file.open(&#39;test/Разные файлы/astros.json&#39;) as file:
        print(file.read())
```

```
# Вывод:
b&#39;{&#34;number&#34;: 10, &#34;people&#34;: [{&#34;craft&#34;: &#34;ISS&#34;, &#34;name&#34;: &#34;Mark Vande Hei&#34;}, {&#34;craft&#34;: &#34;ISS&#34;, &#34;name&#34;: &#34;Pyotr Dubrov&#34;}, {&#34;craft&#34;: &#34;ISS&#34;, &#34;name&#34;: &#34;Thomas Pesquet&#34;}, {&#34;craft&#34;: &#34;ISS&#34;, &#34;name&#34;: &#34;Megan McArthur&#34;}, {&#34;craft&#34;: &#34;ISS&#34;, &#34;name&#34;: &#34;Shane Kimbrough&#34;}, {&#34;craft&#34;: &#34;ISS&#34;, &#34;name&#34;: &#34;Akihiko Hoshide&#34;}, {&#34;craft&#34;: &#34;ISS&#34;, &#34;name&#34;: &#34;Anton Shkaplerov&#34;}, {&#34;craft&#34;: &#34;Shenzhou 13&#34;, &#34;name&#34;: &#34;Zhai Zhigang&#34;}, {&#34;craft&#34;: &#34;Shenzhou 13&#34;, &#34;name&#34;: &#34;Wang Yaping&#34;}, {&#34;craft&#34;: &#34;Shenzhou 13&#34;, &#34;name&#34;: &#34;Ye Guangfu&#34;}], &#34;message&#34;: &#34;success&#34;}&#39;
```

Метод `ZipFile.open()` открывает файл в бинарном виде, поэтому перед выводом стоит символ `b`, а значит перед нами бинарная строка. Чтобы преобразовать байты в строку, воспользуемся методом `decode()` с указанием кодировки (`&#39;utf-8&#39;`):
```py
from zipfile import ZipFile

with ZipFile(&#39;test.zip&#39;) as zip_file:
    with zip_file.open(&#39;test/Разные файлы/astros.json&#39;) as file:
        print(file.read().decode(&#39;utf-8&#39;))
```

```
# Вывод:
{&#34;number&#34;: 10, &#34;people&#34;: [{&#34;craft&#34;: &#34;ISS&#34;, &#34;name&#34;: &#34;Mark Vande Hei&#34;}, {&#34;craft&#34;: &#34;ISS&#34;, &#34;name&#34;: &#34;Pyotr Dubrov&#34;}, {&#34;craft&#34;: &#34;ISS&#34;, &#34;name&#34;: &#34;Thomas Pesquet&#34;}, {&#34;craft&#34;: &#34;ISS&#34;, &#34;name&#34;: &#34;Megan McArthur&#34;}, {&#34;craft&#34;: &#34;ISS&#34;, &#34;name&#34;: &#34;Shane Kimbrough&#34;}, {&#34;craft&#34;: &#34;ISS&#34;, &#34;name&#34;: &#34;Akihiko Hoshide&#34;}, {&#34;craft&#34;: &#34;ISS&#34;, &#34;name&#34;: &#34;Anton Shkaplerov&#34;}, {&#34;craft&#34;: &#34;Shenzhou 13&#34;, &#34;name&#34;: &#34;Zhai Zhigang&#34;}, {&#34;craft&#34;: &#34;Shenzhou 13&#34;, &#34;name&#34;: &#34;Wang Yaping&#34;}, {&#34;craft&#34;: &#34;Shenzhou 13&#34;, &#34;name&#34;: &#34;Ye Guangfu&#34;}], &#34;message&#34;: &#34;success&#34;}
```

---
### Запись в архив
Чтобы записать файл в архив, нужно как минимум открыть его для записи (`mode=&#39;w&#39;` — для записи с удалением уже имеющегося содержимого; или `mode=&#39;a&#39;` — для записи в конец, сохраняя содержимое). Далее, используем метод `write()`:
```py
from zipfile import ZipFile

with ZipFile(&#39;archive.zip&#39;, mode=&#39;w&#39;) as zip_file:
    zip_file.write(&#39;program.py&#39;)
    zip_file.write(&#39;lse.jpeg&#39;)
    print(zip_file.namelist())
```

{{&lt; admonition danger &gt;}}
Если файлы для записи в архив не будут найдены, то возникнет ошибка (исключение) `FileNotFoundError`
{{&lt; /admonition &gt;}}

Метод `write()` может принимать еще один строковый аргумент, задающий новое имя файла в архиве.

---
### Извлечение содержимого
Если требуется извлечь отдельные файлы, то используется метод `extract()`, он принимает два аргумента: название файла и путь, по которому требуется извлечь файл. Если путь не указывать, то файл будет извлечен в папку, где находится файл с программой.  
Следующий код извлекает файлы из архива `test.zip`
```py
from zipfile import ZipFile

with ZipFile(&#39;test.zip&#39;) as zip_file:
    zip_file.extract(&#39;test/Картинки/avatar.png&#39;)
    zip_file.extract(&#39;test/Программы/image_util.py&#39;)
    zip_file.extract(&#39;lse.jpeg&#39;)
```

Если требуется извлечь все содержимое архива, то используется метод `extractall()`, он принимает в качестве аргумента путь, по которому требуется извлечь все файлы. Если путь не указывать, то файл будет извлечен в папку, где находится файл с программой.

---
## Сериализация и десериализация
Преобразование переменных программы (Python-объектов) в формат для хранения называется «сериализацией», а обратное преобразование — «десериализацией».

Сериализация объектов часто используется для:
- сохранения состояния программы (то есть некоторых её объектов) между запусками
- передачи данных между различными программами (например, по сети)

Главная идея состоит в том, что сериализованный формат — набор байт или строка, которую можно легко сохранить на диск или передать другой программе, в отличие от самого объекта. А значит, задача сохранения объекта/группы объектов при этом сводится к простой задаче сохранения набора байт или строки.

---
## Модуль `pickle`
Помимо сериализации в формат `json` мы также можем использовать бинарную сериализацию, то есть сериализацию в байты. Для этого в Python используется модуль pickle. Интерфейс взаимодействия с модулем pickle абсолютно такой же, как и для модуля json. Мы будем использовать четыре основных функции: `dump(), dumps(), load(), loads()`.

{{&lt; admonition info &gt;}}
Модуль `pickle` сериализует и десериализует данные быстрее чем модуль `json`.
{{&lt; /admonition &gt;}}

Модуль `pickle` может сериализовывать:
1. Все встроенные типы данных (`bool, int, float, complex, str, None`);
2. Списки, кортежи, словари и множества, содержащие любую комбинацию встроенных типов данных (но не генераторы);
3. Списки, кортежи, словари и множества, содержащие любую комбинацию списков, кортежей, словарей и множеств;
4. Функции (но не `lambda`-функции), классы и экземпляры классов.

Разберём функции модуля:

* Функция `dump()` модуля `pickle` принимает сериализуемый Python объект, сериализует его в бинарный, Python-зависимый формат, используя протокол `pickle`, и сохраняет его в открытый для записи бинарный файл.

Следующий код создает файл `file.pkl`, содержащий бинарное представление объекта `obj`:
```py
import pickle

obj = {&#39;Python&#39;: 1991, &#39;Java&#39;: 1995, &#39;C#&#39;: 2002}

with open(&#39;file.pkl&#39;, &#39;wb&#39;) as file:
    pickle.dump(obj, file)
```

* Функция `dumps()` выполняет такую же сериализацию, как и функция `dump()`. Но вместо того чтобы сохранять сериализованные данные в открытый для записи бинарный файл, она просто возвращает эти сериализованные данные.
```py
import pickle

obj = {&#39;Python&#39;: 1991, &#39;Java&#39;: 1995, &#39;C#&#39;: 2002}
binary_obj = pickle.dumps(obj)

print(binary_obj)
print(type(binary_obj))
```

```
# Вывод:
b&#39;\x80\x03}q\x00(X\x06\x00\x00\x00Pythonq\x01M\xc7\x07X\x04\x00\x00\x00Javaq\x02M\xcb\x07X\x02\x00\x00\x00C#q\x03M\xd2\x07u.&#39;
&lt;class &#39;bytes&#39;&gt;
```

Поскольку протокол `pickle` использует бинарный формат данных, функция `dumps()` возвращает объект типа `bytes`.

{{&lt; admonition info &gt;}}
Тип данных `bytes` — это неизменяемые последовательности отдельных байтов. Синтаксис для байтовых литералов в основном такой же, как и для строковых литералов, за исключением того, что добавляется префикс `b`.
{{&lt; /admonition &gt;}}

* Функция `load()` принимает файловый объект, читает из него сериализованные данные, десериализует их в Python-объект и возвращает полученный Python-объект.
```py
import pickle

with open(&#39;file.pkl&#39;, &#39;rb&#39;) as file:     # используется файл полученный на предыдущем шаге
    obj = pickle.load(file)
    print(obj)
    print(type(obj))
```

```
# Вывод:
{&#39;Python&#39;: 1991, &#39;Java&#39;: 1995, &#39;C#&#39;: 2002}
&lt;class &#39;dict&#39;&gt;
```

* Функция `loads()` выполняет такую же десериализацию, как и функция `load()`. Но вместо того чтобы принимать файловый объект, она принимает объект типа `bytes`, содержащий сериализованные данные.
```py
import pickle

obj = {&#39;Python&#39;: 1991, &#39;Java&#39;: 1995, &#39;C#&#39;: 2002}
binary_obj = pickle.dumps(obj)
new_obj = pickle.loads(binary_obj)

print(new_obj)

print(obj == new_obj)
print(obj is new_obj)       # проверка на идентичность
```

```
# Вывод:
{&#39;Python&#39;: 1991, &#39;Java&#39;: 1995, &#39;C#&#39;: 2002}
True
False
```

Интересно то, что объекты `obj` и `new_obj` имеют одинаковое содержимое, но по сути не идентичны.

{{&lt; admonition info &gt;}}
Протокол `pickle` зависит от Python и не совместим с другими языками программирования. Если необходима совместимость с другими языками программирования, то следует использовать JSON сериализацию.
{{&lt; /admonition &gt;}}

---

> Автор: [NoisyCake](https://t.me/noisycake)  
> URL: http://localhost:1313/notes/zip_pkl/  

