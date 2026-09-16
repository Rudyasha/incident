# Задача 3. Тест-кейсы

<div class="suite-summary" markdown>

**POST** `/api/v1/exchanges`

8 кейсов · 4 позитивных · 4 негативных · Статус: не запускались

</div>

## Подход к тестированию

Из-за ограничения задания в восемь тест-кейсов я сфокусировалась на основном параметре операции обмена — количестве чистых и грязных ковров. Для него использованы классы эквивалентности и анализ граничных значений.

В полном наборе дополнительно использовала бы другие техники для остальных параметров и состояний функции: классы эквивалентности для обязательности, типов и форматов полей; таблицу решений для связей driver–route–point–client и предусловий; тестирование переходов состояний для статусов точки; негативное REST-тестирование для authentication, HTTP methods и Content-Type; а также проверки по рискам для повторного `exchange_id`, timeout и retry.

В названиях кейсов я предпочитаю указывать значимые поля и их значения, а также отмечать позитивный или негативный сценарий. Здесь тип вынесен в отдельную метку рядом с приоритетом.

## Общие условия

- Подготовлен валидный запрос по всем полям, кроме изменяемых в конкретном кейсе.
- Для каждого запуска используется новый уникальный `exchange_id`; обмен ещё не завершён.
- Доступны API, БД, Kafka и логи обработки. События сопоставляются по `exchange_id`, topic и partition.
- Тестовые данные ниже — фрагмент запроса, остальные поля остаются валидными.

!!! note "Ожидания, которые нужно согласовать"
    Набор составлен для предполагаемого контракта: оба поля обязательны, принимают целые неотрицательные числа, суммарное количество не превышает 64. Допустимость `0/0`, неравных количеств и максимума в одном поле требует ответа аналитика. Для ошибок валидации `422` указан как предлагаемый код, а не подтверждённое требование. [Вопросы аналитику](task-1.md#4).

## Реестр кейсов

| ID | Сценарий | Тип | Приоритет |
|---|---|---|---|
| [TC-01](#tc-01) | Минимальные значения: clean_mats=0, dirty_mats=0 | Positive / Boundary | P1 |
| [TC-02](#tc-02) | Неравные количества: clean_mats=31, dirty_mats=32 | Positive / Boundary | P1 |
| [TC-03](#tc-03) | Максимальная сумма: clean_mats=32, dirty_mats=32 | Positive / Boundary | P0 |
| [TC-04](#tc-04) | Максимум в одном поле: clean_mats=0, dirty_mats=64 | Positive / Boundary | P1 |
| [TC-05](#tc-05) | Обязательное поле clean_mats отсутствует | Negative | P1 |
| [TC-06](#tc-06) | Поле dirty_mats передано как null | Negative | P1 |
| [TC-07](#tc-07) | Превышение суммы: clean_mats=32, dirty_mats=33 | Negative / Boundary | P0 |
| [TC-08](#tc-08) | Строка вместо integer: clean_mats="8", dirty_mats=8 | Negative | P1 |

<div class="test-case" markdown>

## TC-01 · Минимальные значения: clean_mats=0, dirty_mats=0 {#tc-01}

<div class="case-tags" markdown>

<span class="case-tag positive">Positive / Boundary</span>
<span class="case-tag priority">P1</span>
<span class="case-tag">Не запускался</span>

</div>

**Техника:** Boundary Value Analysis + Requirement Analysis

### Тестовые данные

```json
{
  "clean_mats": 0,
  "dirty_mats": 0
}
```

**Сумма:** 0.

*Ожидание при условии, что обмен с нулевыми количествами разрешён бизнес-правилами.*

### Шаги

1. Подготовить валидный запрос по остальным полям.
2. Установить `clean_mats=0`, `dirty_mats=0`.
3. Выполнить `POST /api/v1/exchanges`.
4. Проверить ответ API, записи в БД и сообщения Kafka по `exchange_id`.

### Ожидаемый результат

- API возвращает `200 OK`.
- Ответ содержит `status=ok` и отправленный `exchange_id`.
- Exchange сохранён в БД со статусом `saved` и переданными количествами.
- В Kafka опубликовано одно сообщение с ключом `exchange_id`.

</div>

<div class="test-case" markdown>

## TC-02 · Неравные количества: clean_mats=31, dirty_mats=32 {#tc-02}

<div class="case-tags" markdown>

<span class="case-tag positive">Positive / Boundary</span>
<span class="case-tag priority">P1</span>
<span class="case-tag">Не запускался</span>

</div>

**Техника:** Boundary Value Analysis

### Тестовые данные

```json
{
  "clean_mats": 31,
  "dirty_mats": 32
}
```

**Сумма:** 63.

*Ожидание при условии, что неравные количества допустимы.*

### Шаги

1. Подготовить валидный запрос по остальным полям.
2. Установить `clean_mats=31`, `dirty_mats=32`.
3. Выполнить `POST /api/v1/exchanges`.
4. Проверить ответ API, записи в БД и сообщения Kafka по `exchange_id`.

### Ожидаемый результат

- API возвращает `200 OK`.
- Ответ содержит `status=ok` и отправленный `exchange_id`.
- Exchange сохранён в БД со статусом `saved` и переданными количествами.
- В Kafka опубликовано одно сообщение с ключом `exchange_id`.

</div>

<div class="test-case" markdown>

## TC-03 · Максимальная сумма: clean_mats=32, dirty_mats=32 {#tc-03}

<div class="case-tags" markdown>

<span class="case-tag positive">Positive / Boundary</span>
<span class="case-tag priority">P0</span>
<span class="case-tag">Не запускался</span>

</div>

**Техника:** Boundary Value Analysis

### Тестовые данные

```json
{
  "clean_mats": 32,
  "dirty_mats": 32
}
```

**Сумма:** 64.

### Шаги

1. Подготовить валидный запрос по остальным полям.
2. Установить `clean_mats=32`, `dirty_mats=32`.
3. Выполнить `POST /api/v1/exchanges`.
4. Проверить ответ API, записи в БД и сообщения Kafka по `exchange_id`.

### Ожидаемый результат

- API возвращает `200 OK`.
- Ответ содержит `status=ok` и отправленный `exchange_id`.
- Exchange сохранён в БД со статусом `saved` и переданными количествами.
- В Kafka опубликовано одно сообщение с ключом `exchange_id`.

</div>

<div class="test-case" markdown>

## TC-04 · Максимум в одном поле: clean_mats=0, dirty_mats=64 {#tc-04}

<div class="case-tags" markdown>

<span class="case-tag positive">Positive / Boundary</span>
<span class="case-tag priority">P1</span>
<span class="case-tag">Не запускался</span>

</div>

**Техника:** Boundary Value Analysis + Equivalence Partitioning

### Тестовые данные

```json
{
  "clean_mats": 0,
  "dirty_mats": 64
}
```

**Сумма:** 64.

*Ожидание при условии, что обмен без чистых ковров допустим и отдельное ограничение dirty_mats не ниже 64.*

### Шаги

1. Подготовить валидный запрос по остальным полям.
2. Установить `clean_mats=0`, `dirty_mats=64`.
3. Выполнить `POST /api/v1/exchanges`.
4. Проверить ответ API, записи в БД и сообщения Kafka по `exchange_id`.
5. Проверить состояние точки: оно соответствует согласованному правилу завершения обмена.

### Ожидаемый результат

- API возвращает `200 OK`.
- Ответ содержит `status=ok` и отправленный `exchange_id`.
- Exchange сохранён в БД со статусом `saved` и переданными количествами.
- В Kafka опубликовано одно сообщение с ключом `exchange_id`.

</div>

<div class="test-case" markdown>

## TC-05 · Обязательное поле clean_mats отсутствует {#tc-05}

<div class="case-tags" markdown>

<span class="case-tag negative">Negative</span>
<span class="case-tag priority">P1</span>
<span class="case-tag">Не запускался</span>

</div>

**Техника:** Equivalence Partitioning

### Тестовые данные

```json
{
  "dirty_mats": 1
}
```

### Шаги

1. Подготовить валидный запрос по остальным полям.
2. Удалить поле `clean_mats` из JSON целиком; установить `dirty_mats=1`. Не передавать пустую строку или `null`.
3. Выполнить `POST /api/v1/exchanges`.
4. Проверить ответ API, записи в БД и сообщения Kafka по `exchange_id`.

### Ожидаемый результат

- API отклоняет запрос с ошибкой валидации; предлагаемый код — `422`, окончательный код определяется контрактом.
- Exchange не сохраняется как успешно выполненный.
- Успешное Kafka-событие не публикуется.

</div>

<div class="test-case" markdown>

## TC-06 · Поле dirty_mats передано как null {#tc-06}

<div class="case-tags" markdown>

<span class="case-tag negative">Negative</span>
<span class="case-tag priority">P1</span>
<span class="case-tag">Не запускался</span>

</div>

**Техника:** Equivalence Partitioning

### Тестовые данные

```json
{
  "clean_mats": 1,
  "dirty_mats": null
}
```

### Шаги

1. Подготовить валидный запрос по остальным полям.
2. Установить `clean_mats=1`, `dirty_mats=null`.
3. Выполнить `POST /api/v1/exchanges`.
4. Проверить ответ API, записи в БД и сообщения Kafka по `exchange_id`.

### Ожидаемый результат

- API отклоняет запрос с ошибкой валидации; предлагаемый код — `422`, окончательный код определяется контрактом.
- Exchange не сохраняется как успешно выполненный.
- Успешное Kafka-событие не публикуется.

</div>

<div class="test-case" markdown>

## TC-07 · Превышение суммы: clean_mats=32, dirty_mats=33 {#tc-07}

<div class="case-tags" markdown>

<span class="case-tag negative">Negative / Boundary</span>
<span class="case-tag priority">P0</span>
<span class="case-tag">Не запускался</span>

</div>

**Техника:** Boundary Value Analysis

### Тестовые данные

```json
{
  "clean_mats": 32,
  "dirty_mats": 33
}
```

**Сумма:** 65.

### Шаги

1. Подготовить валидный запрос по остальным полям.
2. Установить `clean_mats=32`, `dirty_mats=33`.
3. Выполнить `POST /api/v1/exchanges`.
4. Проверить ответ API, записи в БД и сообщения Kafka по `exchange_id`.

### Ожидаемый результат

- API отклоняет запрос с ошибкой валидации; предлагаемый код — `422`, окончательный код определяется контрактом.
- Exchange не сохраняется как успешно выполненный.
- Успешное Kafka-событие не публикуется.

</div>

<div class="test-case" markdown>

## TC-08 · Строка вместо integer: clean_mats="8", dirty_mats=8 {#tc-08}

<div class="case-tags" markdown>

<span class="case-tag negative">Negative</span>
<span class="case-tag priority">P1</span>
<span class="case-tag">Не запускался</span>

</div>

**Техника:** Equivalence Partitioning + Contract Testing

### Тестовые данные

```json
{
  "clean_mats": "8",
  "dirty_mats": 8
}
```

### Шаги

1. Подготовить валидный запрос по остальным полям.
2. Передать `clean_mats` строкой `"8"`, а `dirty_mats` — числом `8`.
3. Выполнить `POST /api/v1/exchanges`.
4. Проверить ответ API, записи в БД и сообщения Kafka по `exchange_id`.

### Ожидаемый результат

- API отклоняет запрос с ошибкой валидации; предлагаемый код — `422`, окончательный код определяется контрактом.
- Exchange не сохраняется как успешно выполненный.
- Успешное Kafka-событие не публикуется.

</div>
