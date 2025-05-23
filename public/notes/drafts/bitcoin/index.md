# Bitcoin


{{&lt; admonition warning &gt;}}
В процессе написания!
{{&lt; /admonition &gt;}}

Статья посвящена объяснению работы криптовалют на примере первой из них — Bitcoin.
&lt;!--more--&gt;

## Блокчейн

Привет! Сегодня я постараюсь объяснить простым языком, как устроены криптовалюты. Начнём с определения блокчейна.

**Блокчейн** в классическом его понимании — это структура данных, представляющая из себя односвязный список. Он состоит из блоков, в каждом из которых присутствует определённая информация и &lt;u&gt;сылка на предыдущий блок&lt;/u&gt; (поэтому и односвязный).

Блокчейн биткоина является частной реализацией упомянутого блокчейна. Отличается он децентрализацией, наличием консенсуса и PoW, о которых мы поговорим чуть позже, а пока посмотрим на содержимое блоков такой системы.

В контексте битка блокчейн представляет из себя список транзакций, где в каждом блоке содержатся:
1. Заголовок
    1. Хэш предыдущего блока
    2. Хэш текущего блока
    3. Метка времени
    4. Nonce
    5. Сложность майнинга
2. Тело
    1. Транзакция 1
    2. Транзакция 2
    3. ...



---

> Автор: [NoisyCake](https://t.me/noisycake)  
> URL: http://localhost:1313/notes/drafts/bitcoin/  

