# Проект DIT (Data Integration Toolkit)

Набор инструментов для автоматизации работы с платформой TOPS и вспомогательных утилит.

## Структура проекта

```
.
├── .github/                          # CI/CD конфигурация
│   └── workflows/
│       └── uz_load_calculator_deploy.yml
│
├── deg/                              # Инструменты отдела DE&G
│   └── uz_load_calculator/           # Калькулятор узловой нагрузки
│       └── uz_load_calculator.html
│
└── tops_at/                          # Автоматизация TOPS
    ├── settings.py                   # Общие настройки (pydantic-settings)
    ├── .env.example                  # Шаблон переменных окружения
    ├── pyproject.toml                # Конфигурация проекта (Python 3.14+)
    ├── requirements.txt              # Зависимости
    ├── uv.lock                       # Lock-файл uv
    │
    ├── check_uploaded_files/         # Проверка фоновых задач импорта
    │   ├── main_async.py             # Основной асинхронный скрипт
    │   ├── main_sync.py              # Устаревшая синхронная версия
    │   ├── service.py                # HTTP-клиент для API TOPS
    │   ├── dataprovider.py           # Клиент Data Provider API
    │   ├── data_transform.py         # Трансформация данных
    │   ├── checks.py                 # Логика проверки задач
    │   ├── write_file.py             # Запись результатов в CSV
    │   ├── test.py                   # Отладочный скрипт
    │   └── artifacts/                # Директория с результатами
    │
    └── generate_rowid_dataset/       # Подготовка данных для upsert
        ├── download_rows_async.py    # Асинхронный сбор row_id
        ├── shuffle.py                # Очистка дубликатов и перемешивание
        ├── upload_data_in_db.py      # Загрузка в БД Датапровайдера
        └── data/                     # CSV-файлы с row_id
```

## Быстрый старт

```bash
# Клонирование
git clone <repo-url> && cd dit

# Настройка окружения для tops_at
cd tops_at
cp .env.example .env
# Отредактировать .env, указав актуальные токен и параметры БД
```

### Через pip

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

### Через uv

```bash
uv venv
source .venv/bin/activate
uv sync
```

## Подробнее о компонентах

| Компонент | Описание |
|-----------|----------|
| [`deg/uz_load_calculator`](deg/uz_load_calculator/uz_load_calculator.html) | Веб-калькулятор узловой нагрузки (автономный HTML) |
| [`tops_at/check_uploaded_files`](tops_at/check_uploaded_files/) | Проверка успешности фонового импорта файлов в TOPS |
| [`tops_at/generate_rowid_dataset`](tops_at/generate_rowid_dataset/) | Подготовка тестовых данных для скрипта upsert |