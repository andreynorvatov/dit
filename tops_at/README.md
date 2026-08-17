# Автоматизация TOPS (tops-at)

Набор скриптов для автоматизации проверки и подготовки данных на платформе [TOPS](https://tops-stage.mos.ru).

## Структура

```
tops_at/
├── settings.py                       # Общие настройки проекта
├── .env.example                      # Шаблон переменных окружения
├── .gitignore                        # Игнорируемые файлы
├── .python-version                   # Версия Python (3.14+)
├── pyproject.toml                    # Конфигурация и зависимости
├── requirements.txt                  # Альтернативный список зависимостей
├── uv.lock                           # Lock-файл для uv
│
├── check_uploaded_files/             # Проверка фоновых задач импорта
│   └── README.md                     # Документация модуля
│
├── generate_rowid_dataset/           # Подготовка данных для upsert
│   └── README.md                     # Документация модуля
│
├── artifacts/                        # Результаты проверок (создаётся)
├── data/                             # CSV-файлы с row_id (создаётся)
├── .venv/                            # Виртуальное окружение
└── .idea/                            # Настройки IDE
```

## Компоненты

### [check_uploaded_files](check_uploaded_files/) — Проверка импорта файлов

_Набор скриптов для верификации успешности фоновых операций по импорту файлов._

- Асинхронно опрашивает API TOPS по каждому task_id и row_id
- Собирает и агрегирует данные из трёх источников (Датапровайдер + task_id + row_id)
- Проверяет статус, длительность выполнения, заполненность полей
- Сохраняет результат в CSV и выводит сводку в консоль
- Опционально отмечает строки как проверенные в Датапровайдере

Подробнее → [check_uploaded_files/README.md](check_uploaded_files/README.md)

### [generate_rowid_dataset](generate_rowid_dataset/) — Подготовка данных для upsert

_Набор скриптов для сбора, очистки и загрузки тестовых данных (row_id)._

Последовательный пайплайн из 3 шагов:
1. **Сбор** — асинхронное скачивание row_id из проекции TOPS
2. **Очистка** — удаление дубликатов и перемешивание
3. **Загрузка** — вставка данных в таблицу Датапровайдера через `COPY FROM`

Подробнее → [generate_rowid_dataset/README.md](generate_rowid_dataset/README.md)

## Настройка окружения

### 1. Переменные окружения

Создать `.env` из шаблона:

```bash
cp .env.example .env
```

Заполнить обязательные поля:

| Переменная | Описание |
|-----------|----------|
| `TOPS_URL` | URL стенда TOPS (по умолчанию `https://tops-stage.mos.ru`) |
| `TOKEN` | Авторизационный токен пользователя |
| `TOPS_DATA_TABLE_ID` | UUID таблицы данных |
| `DATAPROVIDER_HOST` | Хост БД Датапровайдера |
| `DATAPROVIDER_PORT` | Порт PostgreSQL (по умолчанию 5432) |
| `DATAPROVIDER_DATABASE` | Имя БД |
| `DATAPROVIDER_USER` | Пользователь БД |
| `DATAPROVIDER_PWD` | Пароль БД |
| `DATAPROVIDER_API_PORT` | Порт Data Provider API (по умолчанию 3000) |
| `TABLE_NAME_CHECK_UPDATE` | Таблица для проверки задач (по умолчанию `tops_checkupdate_pp`) |

### 2. Установка зависимостей

**Через pip:**

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

**Через uv (рекомендуется):**

```bash
uv venv
source .venv/bin/activate
uv sync
```

## Общие ресурсы

- [settings.py](settings.py) — настройки проекта (pydantic-settings)
- [.env.example](.env.example) — шаблон файла с переменными окружения
- [requirements.txt](requirements.txt) — зависимости для pip
- [pyproject.toml](pyproject.toml) — метаданные и зависимости проекта