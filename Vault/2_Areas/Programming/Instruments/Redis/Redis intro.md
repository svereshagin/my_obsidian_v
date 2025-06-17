### 🌟 Redis: Основы, Принципы и Компоненты

**Redis** (Remote Dictionary Server) — это in-memory NoSQL-база данных со структурами данных ключ-значение. Используется как кэш, брокер сообщений и хранилище реального времени.

---

### 🔑 Ключевые Особенности
1. **In-Memory Storage**  
   Данные хранятся в оперативной памяти → скорость доступа ~100,000 операций/сек.
2. **Дисковая Persistence**  
   Опции сохранения данных:
   - **RDB** (снимки состояния)
   - **AOF** (лог операций)
3. **Поддержка структур данных**  
   Не только строки, а 10+ типов.
4. **Режимы работы**  
   Standalone, Sentinel (отказоустойчивость), Cluster (шардирование).

---

### 🧩 Основные Компоненты и Структуры Данных

#### 1. Strings (Строки)
Максимум 512 МБ. Используются для кэша, счётчиков.
```python
import redis
r = redis.Redis()

r.set("user:1000:name", "Alice")
print(r.get("user:1000:name"))  # b'Alice'

# Инкремент счётчика
r.incr("page:views:home")
```

#### 2. Hashes (Хеши)
Хранение объектов. Эффективно для частичного обновления.
```python
r.hset("user:1000", mapping={
    "name": "Alice",
    "email": "alice@example.com",
    "age": 30
})
print(r.hget("user:1000", "email"))  # b'alice@example.com'
```

#### 3. Lists (Списки)
Двусторонние очереди (deque). Для логов, очередей задач.
```python
# Добавление в очередь
r.lpush("tasks", "send_email")
r.rpush("tasks", "generate_report")

# Обработка
task = r.rpop("tasks")
print(task)  # b'generate_report'
```

#### 4. Sets (Множества)
Уникальные неупорядоченные элементы. Для тегов, анализа.
```python
r.sadd("article:123:tags", "python", "redis", "database")
print(r.smembers("article:123:tags"))  # {b'redis', b'database', b'python'}

# Пересечение множеств
r.sinter("tag:python:articles", "tag:redis:articles")
```

#### 5. Sorted Sets (Упорядоченные множества)
Элементы с score для сортировки. Для рейтингов, лидербордов.
```python
r.zadd("leaderboard", {"Alice": 100, "Bob": 85, "Charlie": 95})

# Топ-2 игроков
print(r.zrevrange("leaderboard", 0, 1, withscores=True)) 
# [(b'Alice', 100.0), (b'Charlie', 95.0)]
```

#### 6. Streams (Потоки)
Для обработки событий и Pub/Sub. Альтернатива Kafka.
```python
# Добавление события
r.xadd("orders", {"user_id": 1000, "product": "book"})

# Чтение
streams = r.xread({"orders": 0}, count=2)
```

#### 7. Bitmaps (Битовые карты)
Экономичное хранение битовых флагов. Для аналитики.
```python
# Установка бита
r.setbit("online:2024-05-01", user_id=1000, value=1)

# Подсчёт активных пользователей
print(r.bitcount("online:2024-05-01"))
```

#### 8. HyperLogLog (HLL)
Вероятностный подсчёт уникальных значений. Погрешность ~0.81%.
```python
r.pfadd("visitors:2024-05-01", "192.168.1.1", "192.168.1.2")
print(r.pfcount("visitors:2024-05-01"))  # ≈2
```

---

### ⚙️ Принципы Работы

#### 1. Партиционирование
- **Шардирование**: Разделение данных между узлами кластера
- **Ключевые хэш-слоты**: 16384 слота, распределение через CRC16

#### 2. Persistence (Сохранение)
| Метод       | Принцип работы                     | Плюсы / Минусы               |
|-------------|-------------------------------------|-------------------------------|
| **RDB**     | Снимки состояния через интервалы   | + Компактность, + Скорость восстановления<br>- Риск потери данных |
| **AOF**     | Лог каждой операции (append-only)  | + Максимальная надежность<br>- Медленное восстановление, Большой размер |

#### 3. Репликация
- **Master-Replica**: Асинхронная репликация
- **Read Scaling**: Реплики обрабатывают чтение

#### 4. Транзакции
```python
# MULTI/EXEC - атомарная группа команд
pipe = r.pipeline()
pipe.multi()
pipe.incr("counter")
pipe.expire("counter", 60)
pipe.execute()
```

---

### 💡 Примеры Использования

#### Кэширование в Django
```python
# settings.py
CACHES = {
    "default": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/1",
    }
}
```

#### Очередь задач с RQ (Redis Queue)
```python
from rq import Queue
from redis import Redis
import jobs

redis_conn = Redis()
queue = Queue(connection=redis_conn)

# Постановка задачи в очередь
queue.enqueue(jobs.send_email, "user@example.com")
```

#### Rate Limiting
```python
def rate_limited(key, limit=10, window=60):
    if r.setnx(key, limit):
        r.expire(key, window)
    
    bucket = r.get(key)
    if int(bucket) > 0:
        r.decr(key)
        return True
    return False  # Лимит исчерпан
```

---

### ⚠️ Ограничения Redis
1. **Данные в памяти**: Требует больших RAM-ресурсов
2. **Нет сложных запросов**: Нет JOIN, сложных индексов
3. **ACID**: Частичная поддержка транзакций (нет rollback)
4. **Макс. размер ключа**: 512 МБ

---

### 🔧 Оптимизация Производительности
1. **Pipelining**: Группировка команд
   ```python
   pipe = r.pipeline()
   for i in range(1000):
       pipe.set(f"key:{i}", i)
   pipe.execute()
   ```
2. **Lua-скрипты**: Атомарные операции на сервере
3. **Ключи с TTL**: Автоочистка через `EXPIRE`
4. **Использование Hash Tags**: Для кластера: `{user1000}.profile`, `{user1000}.contacts`

Redis — идеальный выбор для сценариев, требующих скорости: кэширование, очереди, реальная аналитика. Для хранения критичных данных комбинируйте с дисковыми СУБД.