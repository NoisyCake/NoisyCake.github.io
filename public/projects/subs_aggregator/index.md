# Subscription Aggregator for 3x-ui Proxy Servers


**Subscription Aggregator for 3x-ui Proxy Servers** — это обратный прокси, который объединяет подписки с нескольких прокси-серверов, работающих на платформе 3x-ui, в одну ссылку. Проект расширяет стандартные возможности подписок 3x-ui и делает их использование более удобным.

&lt;!--more--&gt;

## Для чего
Платформа 3x-ui не позволяет объединить несколько серверов, как, например, Marzban. Это приложение частично решает проблему, предоставляя единую ссылку для доступа к подпискам с нескольких прокси-серверов, облегчая подключение для пользователей.

## Как работает
Приложение получает запрос с subscription_id и дублирует его на сервера 3x-ui. Каждый сервер возвращает конфигурацию в виде base64 строки. Приложение декодирует полученные данные, объединяет их и снова кодирует в base64. Пользователю нужно вставить лишь один URL, ссылающийся на Nginx сервер, чтобы получить доступ ко всем конфигурациям.

Так, если отправить запрос на `https://example.com/sub/test`, приложение отправит соответствующие запросы на получение подписок, к примеру на `https://3x-ui_server/subscription/test` и `https://another_server/podpiska/test`. Если подписки найдены, приложение получает ответы и объединяет их, возвращая пользователю base64 строку.

## Стек технологий
🐍🚀 Backend: Python &#43; FastAPI &#43; httpx  

🌐🔒 Сервер: Nginx  

🐳🛠️ Инфраструктура: Docker

## Основные фичи
✅ **Объединение подписок** — Автоматическое извлечение данных с нескольких прокси-серверов и их объединение в одну ссылку

✅ **Асинхронная работа** — Использование асинхронного Python (FastAPI), а также Nginx

✅ **Безопасность** — Обязательное использование HTTPS и редирект с HTTP

✅ **Простота развёртывания** — Благодаря Docker

---
## Исходный код
Все исходники доступны на [GitHub](https://github.com/NoisyCake/3x-ui_subscriptions_aggregator#). Далее разберём логику проекта, а именно `main.py` файл.

### Импорт библиотек  
- `os` для работы с переменными окружения; 
- `httpx` для асинхронных HTTP-запросов;
- `base64` для кодирования и декодирования данных соответствующего формата;
- `logging` для логирования ошибок;
- `asyncio.gather` для параллельного выполнения асинхронных задач;
- `typing.Optional` для аннотации, поскольку в качестве Docker-образа используется старая легковесная версия Python, в которой ещё не поддерживаются последнии фичи аннотации;
- `dotenv.load_dotenv` для загрузки переменных окружения;
- `fastapi` для создания веб-приложения и обработки запросов.

```py
import os
import httpx
import base64
import logging
from asyncio import gather
from typing import Optional
from dotenv import load_dotenv
from fastapi import FastAPI, Response, HTTPException
```

### Настройка логера  
Здесь настраивается система логирования. В файл script_log.log записываются предупреждения и ошибки в формате {дата_и_время уровень_логирования сообщение}
```py
logging.basicConfig(
    level=logging.WARNING, filename=&#39;script_log.log&#39;,
    format=&#39;%(asctime)s %(levelname)s: %(message)s&#39;
)
logger = logging.getLogger(__name__)
```

### Инициализация FastAPI  
Первая строка создаёт экземпляр веб-приложения, а вторая загружает переменные окружения из файла `.env`.
```py
app = FastAPI()
load_dotenv()
```

### Функция `fetch_subscription`
Отправляет асинхронный GET-запрос к sub_url/sub_id. В случае успешного ответа декодирует base64 в бинарный формат, иначе — возвращает `None` и записывает ошибку в логи.
```py
async def fetch_subscription(client, sub_url: str, sub_id: str) -&gt; Optional[bytes]:
    try:
        sub = await client.get(f&#39;{sub_url}{sub_id}&#39;, timeout=5)
        sub.raise_for_status()
        return base64.b64decode(sub.text)
    except httpx.HTTPError as e:
            logger.error(f&#34;Can&#39;t get subscription url from {sub_url}{sub_id}: {str(e)}&#34;)
    except ValueError as e:
            logger.error(f&#34;Can&#39;t decode base64 for {sub_url}{sub_id}: {str(e)}&#34;)
```

### Функция `merge_subscriptions`
1. Получает список URL из переменной окружения;
1. Проверяет наличие хотя бы одного сервера;
1. Создаёт асинхронный клиент;
1. Параллельно запрашивает подписки со всех серверов;
1. Фильтрует результаты, чтобы убрать неудачные запросы;
1. Объединяет и кодирует данные;
1. Возвращает ответ в виде голого текста.
```py
@app.get(&#39;/sub/{sub_id}&#39;)
async def merge_subscriptions(sub_id: str) -&gt; Response:
    sub_urls = os.getenv(&#39;SUB_URLS&#39;).split()
    
    if not sub_urls:
        logger.error(&#34;Servers not found&#34;)
        raise HTTPException(status_code=500, detail=&#34;Servers not found&#34;)
    
    
    async with httpx.AsyncClient() as client:
        decoded_subs = [fetch_subscription(client, sub_url, sub_id) for sub_url in sub_urls]
        results = await gather(*decoded_subs)
        data = [result for result in results if result is not None]
        
        if not data:
            logger.error(&#34;No subscriptions available&#34;)
            raise HTTPException(status_code=500, detail=&#34;No subscriptions available&#34;)    
        
        merged_subs = b&#39;&#39;.join(data)
        global_sub = base64.b64encode(merged_subs)
        
        return Response(content=global_sub, media_type=&#39;text/plain&#39;)
```

---
## Установка
Процесс установки подробно описан в [README.md](https://github.com/NoisyCake/3x-ui_subscriptions_aggregator#) проекта.

## Ссылки
**GitHub-репозиторий проекта**: https://github.com/NoisyCake/3x-ui_subscriptions_aggregator#  
**Автор**: https://t.me/noisycake  
**ТКГ**: https://t.me/hfk_blog  
**Teletype**: https://teletype.in/@noisycake

## Лицензия
Проект распространяется под лицензией MIT. Подробности в файле [LICENSE](https://github.com/NoisyCake/3x-ui_subscriptions_aggregator/blob/main/LICENSE).

---

> Автор: [NoisyCake](https://t.me/noisycake)  
> URL: http://localhost:1313/projects/subs_aggregator/  

