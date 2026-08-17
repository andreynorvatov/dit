# check_uploaded_files — Проверка фоновых задач импорта

Набор скриптов для верификации успешности фоновых операций по импорту файлов в платформу TOPS.

## Назначение

Скрипты проверяют, что после загрузки файла через интерфейс TOPS:
- фоновая задача импорта завершилась со статусом `COMPLETED`
- длительность выполнения задачи не превышает 20 секунд
- в данных корректно заполнены поля `email_check` и `mob_check`
- данные `row_id` в сервисе соответствуют ожидаемым

## Архитектура

```
main_async.py (точка входа)
    │
    ├── dataprovider.py      ← синхронные запросы к Data Provider API
    │
    ├── service.py           ← асинхронные HTTP-запросы к API TOPS
    │
    ├── data_transform.py    ← извлечение и агрегация данных
    │
    ├── checks.py            ← валидация результатов задач
    │
    └── write_file.py        ← запись результатов в CSV
```

## Поток выполнения

1. **Подсчёт записей** — через [`dataprovider.count_data_size()`](dataprovider.py:14) определяется количество task_id для проверки
2. **Получение данных из Датапровайдера** — [`dataprovider.get_data_from_dataprovider()`](dataprovider.py:30) загружает записи из таблицы
3. **Запрос данных task_id из TOPS** — асинхронный вызов `/api/mona/background_tasks/{task_id}` через [`service.get_data_from_service()`](service.py)
4. **Запрос данных row_id из TOPS** — асинхронный вызов `/api/chiara/projection_data/{projection_id}/{row_id}`
5. **Агрегация** — [`data_transform.union_data_dicts()`](data_transform.py:68) объединяет данные из трёх источников
6. **Запись CSV** — [`write_file.generate_file()`](write_file.py:30) сохраняет результат в `artifacts/`
7. **Валидация** — [`checks.check_task_data()`](checks.py:2) проверяет статусы, длительность, email_check, mob_check
8. **Отметка проверенных** — опционально обновляет `read_line = true` в Датапровайдере
9. **Генерация SQL** — формируется запрос для удаления обработанных строк

## Запуск

```bash
cd tops_at
source .venv/bin/activate
python -m check_uploaded_files.main_async
```

### Настройки в [`main_async.py`](main_async.py)

Переменные в блоке `if __name__ == "__main__":`

| Переменная | Назначение | Пример |
|-----------|-----------|--------|
| `START_DATE` | Начало интервала (timestamp мс) или `None` | `"1776696816117"` |
| `END_DATE` | Конец интервала (timestamp мс) или `None` | `"1776762643345"` |
| `MAX_TASK_ID` | Лимит записей за один запуск | `300` |
| `MAX_CONCURRENT_HTTP_REQUESTS` | Кол-во параллельных HTTP-запросов | `10` |
| `MARK_CHECKED_IN_DATAPROVIDER` | Отмечать строки как проверенные | `True` / `False` |

### Результат

- CSV-файл в папке [`artifacts/`](artifacts/) с именем `check_YYYY-MM-DD_HH-MM-SS.csv`
- Вывод в консоль: количество проблемных задач и детали ошибок

## Файлы

| Файл | Назначение |
|------|-----------|
| [`main_async.py`](main_async.py) | Основной скрипт (асинхронная версия) |
| [`main_sync.py`](main_sync.py) | Устаревшая синхронная версия (`deprecated`) |
| [`service.py`](service.py) | Асинхронный HTTP-клиент для API TOPS с ограничением параллельных запросов |
| [`dataprovider.py`](dataprovider.py) | Синхронный клиент Data Provider API (PostgREST) |
| [`data_transform.py`](data_transform.py) | Извлечение и объединение данных из task_id и row_id |
| [`checks.py`](checks.py) | Логика валидации задач |
| [`write_file.py`](write_file.py) | Запись aggregated-данных в CSV |
| [`test.py`](test.py) | Отладочный скрипт для тестирования API TOPS |
| [`artifacts/`](artifacts/) | Директория с результатами проверок |