# Lincore · QA investigation

Постраничная документация по Задаче 1: гипотезы, анализ артефактов, предварительное заключение, вопросы аналитику. Боковая навигация, поиск на русском, светлая/тёмная темы и небольшая архитектурная схема и одна диаграмма последовательности.

**Статус:** содержание подготовлено по предоставленной переписке. Оригинальные вложения пока не сверены; см. [источники](docs/sources.md).

## Читать в GitHub

- [О задании](docs/index.md)
- [Задача 1. Анализ инцидента](docs/task-1.md)
- [Задача 2. Баг-репорт](docs/task-2.md) — ожидает исходного условия
- [Задача 3. Тест-кейсы](docs/task-3.md) — ожидает исходного условия
- [Исходные материалы](docs/sources.md)

## Локальный просмотр

```sh
python3 -m venv .venv
.venv/bin/pip install -r requirements.txt
.venv/bin/mkdocs serve
```

Открыть http://127.0.0.1:8000. Проверка сборки: `.venv/bin/mkdocs build --strict`.

## Публикация на GitHub Pages

1. Создать репозиторий и загрузить файлы проекта в ветку `main` (без `.venv` и `site`).
2. В репозитории открыть **Settings → Pages → Build and deployment → Source: GitHub Actions**.
3. Запустить workflow **Publish documentation** через Actions или отправить следующий commit в `main`.
4. Готовую ссылку смотреть в результате job `deploy` и Settings → Pages.

Workflow включён в проект. Сайт: https://rudyasha.github.io/incident/ — доступен после успешного выполнения workflow.

## Редактирование

Тексты лежат в `docs/`, порядок страниц — в `mkdocs.yml`, оформление — в `docs/assets/styles.css`. Схемы редактируются текстом внутри блоков `mermaid`.
