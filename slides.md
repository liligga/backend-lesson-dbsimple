---
theme: seriph
background: https://cover.sli.dev
title: Базы данных, создание таблиц, запросы
class: text-center
highlighter: shiki
transition: slide-left
mdc: true
---

## Базы данных, создание таблиц, запросы

---
layout: center
---

## План

<v-clicks>

- Основы
- Запрос для создания таблицы результатов опросов
- Общий класс для работы с базой данных
- Подключение к базе при запуске бота
- Сохранение результатов опросов

</v-clicks>

---
layout: center
---

<TwoColumns>
    <template v-slot:header>
        Что мы можем делать с таблицами, и данными?
    </template>
    <template v-slot:left>
        <ul>
            <v-click at=1><li>Создание таблиц</li></v-click>
            <v-click at=3><li>Удаление таблиц</li></v-click>
            <v-click at=5><li>Внесение данных</li></v-click>
            <v-click at=7><li>Изменение данных</li></v-click>
            <v-click at=9><li>Удаление данных</li></v-click>
        </ul>
    </template>
    <template v-slot:right>
        <ul class="codes">
            <v-click at=2><li><code>CREATE TABLE survey_results (...)</code></li></v-click>
            <v-click at=4><li><code>DROP TABLE survey_results</code></li></v-click>
            <v-click at=6><li><code>INSERT INTO survey_results (...) VALUES (...)</code></li></v-click>
            <v-click at=8><li><code>UPDATE survey_results SET ... WHERE ...</code></li></v-click>
            <v-click at=10><li><code>DELETE FROM survey_results WHERE ...</code></li></v-click>
        </ul>
    </template>
</TwoColumns>

---

## А как работать с SQL в Python?

```python {all|1-4|6-7|9-16|all}{lines:true}
import sqlite3

# подключаемся к базе данных
conn = sqlite3.connect('db.sqlite')

# создаем курсор для выполнения запросов
cur = conn.cursor()

# создаем таблицу
cur.execute('''
    CREATE TABLE survey_results (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT
    )
''')
conn.commit()
```

---

## Сделаем такой запрос асинхронным

<v-clicks>

- Асинхронные запросы к БД ускоряют работу бота
- Устанавливаем библиотеку `aiosqlite`: `pip install aiosqlite`

</v-clicks>

---

## Сделаем такой запрос асинхронным

````md magic-move {lines:true}
```python
import sqlite3

# подключаемся к базе данных
conn = sqlite3.connect('db.sqlite')

# создаем курсор для выполнения запросов
cur = conn.cursor()

# создаем таблицу
cur.execute('''
    CREATE TABLE survey_results (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT
    )
''')
conn.commit()
```
```python
import aiosqlite

# подключаемся к базе данных
async with aiosqlite.connect('db.sqlite') as conn:
    # создаем таблицу
    await conn.execute('''
        CREATE TABLE survey_results (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT
        )
    ''')
    await conn.commit()
```
```python
import aiosqlite

class Database:

    async def create_tables(self):
        # подключаемся к базе данных
        async with aiosqlite.connect('db.sqlite') as conn:
            # создаем таблицу
            await conn.execute('''
                CREATE TABLE survey_results (
                    id INTEGER PRIMARY KEY AUTOINCREMENT,
                    name TEXT
                )
            ''')
            await conn.commit()
```
````

---

## Класс для работы с базой данных

````md magic-move {lines:true}
```python
import aiosqlite

class Database:

    async def create_tables(self):
        # подключаемся к базе данных
        async with aiosqlite.connect('db.sqlite') as conn:
            # создаем таблицу
            await conn.execute('''
                CREATE TABLE survey_results (
                    id INTEGER PRIMARY KEY AUTOINCREMENT,
                    name TEXT
                )
            ''')
            await conn.commit()
```
```python
import aiosqlite

class Database:
    def __init__(self, path):
        self.path = path

    async def create_tables(self):
        async with aiosqlite.connect(self.path) as conn:
            async with conn.cursor() as cur:
                # создание всех таблиц
                await cur.execute('''
                    CREATE TABLE survey_results (
                        id INTEGER PRIMARY KEY AUTOINCREMENT,
                        name TEXT
                    )
                ''')
                # здесь может быть создание других таблиц
                # которые нам нужны
                await conn.commit()


database = Database('db.sqlite')
```
````

---

## Сохранение результатов опросов

````md magic-move {lines:true}
```python
import sqlite3

conn = sqlite3.connect('db.sqlite')

conn.execute(
    "INSERT INTO survey_results (name) VALUES (?)",
    ('John',)
)
conn.commit()
```
```python
import aiosqlite

async with aiosqlite.connect('db.sqlite') as conn:
    await conn.execute(
        "INSERT INTO survey_results (name) VALUES (?)",
        ('John',)
    )
    await conn.commit()
```
```python
class Database:
    async def execute(self):
        async with aiosqlite.connect(self.path) as conn:
            await conn.execute(
                "INSERT INTO survey_results (name) VALUES (?)",
                ('John',)
            )
            await conn.commit()


database = Database('db.sqlite')
await database.execute()
```
```python
class Database:
    async def execute(self, query: str, params: tuple = ()):
        async with aiosqlite.connect(self.path) as conn:
            await conn.execute(query, params)
            await conn.commit()


database = Database('db.sqlite')
await database.execute(
    "INSERT INTO survey_results (name) VALUES (?)",
    ('John',)
)
```
```python
class Database:
    async def execute(self, query: str, params: tuple = ()):
        async with aiosqlite.connect(self.path) as conn:
            await conn.execute(query, params)
            await conn.commit()


database = Database('db.sqlite')
await database.execute(
    "INSERT INTO survey_results (name) VALUES (?)",
    ('John',)
)
await database.execute(
    "INSERT INTO survey_results (name) VALUES (?)",
    ('Igor',)
)
```
````
