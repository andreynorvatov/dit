# deg — Инструменты отдела DE&G

Утилиты и инструменты, разработанные для отдела DE&G (Data Engineering & Governance).

## Компоненты

### [uz_load_calculator](uz_load_calculator/uz_load_calculator.html)

Веб-калькулятор для расчёта узловой (нагрузочной) нагрузки. Автономный HTML-файл, не требует серверной части.

**Возможности:**
- Расчёт нагрузочных показателей по узлам
- Интерактивная таблица с редактируемыми ячейками
- Визуализация данных через Chart.js (графики и диаграммы)
- Выбор типов узлов через выпадающий список
- Подсветка выбранных строк

**Технологии:**
- Чистый HTML/CSS/JavaScript (один файл)
- [Chart.js 4.4.7](https://www.chartjs.org/) — построение графиков (CDN)
- Адаптивный дизайн (Flexbox)

**Деплой:** Автоматический деплой через GitHub Actions — см. [.github/workflows](../.github/workflows/uz_load_calculator_deploy.yml)

**Локальный запуск:** Просто откройте [`uz_load_calculator.html`](uz_load_calculator/uz_load_calculator.html) в браузере.