# Lincore · QA investigation

Постраничная документация по Задаче 1: гипотезы, анализ артефактов, предварительное заключение, вопросы аналитику. Боковая навигация, поиск на русском, светлая/тёмная темы и четыре Mermaid-схемы.

**Статус:** содержание подготовлено по предоставленной переписке. Оригинальные вложения пока не сверены; см. [источники](docs/sources.md).

## Читать в GitHub

- [Обзор](docs/index.md)
- [Контекст](docs/incident/context.md)
- [Flow и схемы](docs/incident/flow.md)
- [Хронология](docs/incident/timeline.md)
- [Гипотезы](docs/incident/hypotheses.md)
- [Артефакты A–C](docs/incident/artifacts.md)
- [Заключение](docs/incident/conclusion.md)
- [Вопросы аналитику](docs/incident/questions.md)

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

Тексты лежат в `docs/incident/`, порядок страниц — в `mkdocs.yml`, оформление — в `docs/assets/styles.css`. Схемы редактируются текстом внутри блоков `mermaid`.
