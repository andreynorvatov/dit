# CI/CD — GitHub Actions

Воркфлоу для автоматического деплоя компонентов проекта на удалённые серверы.

## Доступные воркфлоу

### `uz_load_calculator_deploy.yml`

Автоматический деплой [калькулятора узловой нагрузки](../deg/uz_load_calculator/uz_load_calculator.html) на удалённый сервер.

**Триггер:** push в ветку `main`, затрагивающий файлы в `deg/uz_load_calculator/**`

**Действия:**
1. `actions/checkout@v4` — чекаут репозитория
2. `webfactory/ssh-agent` — настройка SSH-ключа из секрета `admin_fin-amzng`
3. `rsync` — синхронизация папки `deg/uz_load_calculator/` на удалённый сервер

**Необходимые секреты (GitHub Secrets):**

| Секрет | Назначение |
|--------|-----------|
| `admin_fin-amzng` | Приватный SSH-ключ для подключения к серверу |
| `REMOTE_USER_FIN_AMZNG` | Пользователь на удалённом сервере |
| `REMOTE_HOST_FIN_AMZNG` | Адрес удалённого сервера |

**Целевая директория на сервере:** `/home/admin/nginx/deg`