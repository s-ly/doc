# Агрегация данных в SQL

Ключевое слово `DISTINCT` позволяет отбирать уникальные записи, т.е. избавляться от всех дубликатов в таблице.

```sql
SELECT DISTINCT user_id FROM user_actions
ORDER BY user_id
```

Ключевое слово `DISTINCT` можно применять сразу к нескольким.

```sql
SELECT
  DISTINCT courier_id,
  order_id
FROM
  courier_actions
ORDER BY
  courier_id,
  order_id
```

# 

# Агрегирующие функции

Агрегирующими функциями называют функции, которые обрабатывают определённый набор строк и возвращают одно обобщающее значение.

Вот несколько таких функций в SQL:

- `COUNT` — считает количество значений в колонке.

- `SUM` — вычисляет сумму значений.  

- `AVG` — вычисляет среднее значение.  

- `MAX` — вычисляет максимальное значение.  

- `MIN` — вычисляет минимальное значение.

```sql
SELECT
  max (price) as max_price,
  min (price) as min_price
FROM
  products
```

Запрос `COUNT` со звёздочкой возвращает количество вообще всех записей в таблице, а запрос с указанием столбца — количество тех записей, где в заданном столбце значения не являются `NULL`:

```sql
SELECT
  COUNT (*) as dates,
  COUNT(birth_date) as dates_not_null
FROM
  users
```

Агрегирующие функции можно применять в сочетании с ключевым словом `DISTINCT`:

```sql
SELECT
  count(user_id) as users,
  count(DISTINCT user_id) as unique_users
FROM
  user_actions
```

В запрос с агрегирующими функциями можно включить оператор `WHERE`:

```sql
SELECT
  COUNT (courier_id) as couriers
FROM
  couriers
WHERE
  sex = 'female'
```

Расчёты по колонке с данными в формате `TIMESTAMP`:

```sql
SELECT
  min(time) as first_delivery,
  max(time) as last_delivery
FROM
  courier_actions
WHERE
  action = 'deliver_order'
```

Агрегирующие функции с условными конструкциями:

```sql
SELECT
  sum(price) as order_price
FROM
  products
WHERE
  name = 'сухарики'
  or name = 'чипсы'
  or name = 'энергетический напиток'
```

# Массивы

Есть простая таблица следующего вида:

```
 _______
| 1 | 2 |
| 3 | 4 |
| 5 | 6 |
 ‾‾‾‾‾‾‾
```

В этой таблице 3 строки и 2 столбца, поэтому её можно описать в виде следующего списка из трёх вложенных в него списков:

`ARRAY[[1,2], [3,4], [5,6]]`

В этом примере количество списков внутри основного списка — это количество строк в таблице, а количество элементов внутри каждого внутреннего списка — это количество столбцов. Считаем длину одного и того же массива с разными размерностями:

```sql
SELECT array_length(ARRAY[[1,2], [3,4], [5,6]], 1)
Результат: 3

SELECT array_length(ARRAY[[1,2], [3,4], [5,6]], 2)
Результат: 2
```

Длина массива `array_length`.

```sql
SELECT
  count(order_id) as orders
FROM
  orders
WHERE
  array_length(product_ids, 1) >= 9
```

# 

# Вункция AGE — Возраст

```sql
SELECT
  AGE(current_date, max(birth_date))::varchar as min_age
FROM
  couriers
WHERE
  sex = 'male'
```

Аргументом агрегирующей функции может быть и более сложная расчётная колонка — например, полученная в результате работы конструкции `CASE`:

```sql
SELECT
  sum(
    CASE
      WHEN name = 'сухарики' THEN price * 3
      WHEN name = 'чипсы' THEN price * 2
      ELSE price
    END
  ) as order_price
FROM
  products
WHERE
  name = 'сухарики'
  or name = 'чипсы'
  or name = 'энергетический напиток'
```

Агрегирующая функция может оказаться на месте аргумента функции:

```sql
SELECT
  round(avg(price), 2) as avg_price
FROM
  products
WHERE
  name LIKE '%чай%'
  and name != 'чайный гриб'
  and name NOT LIKE '%иван-чай%'
  or name LIKE '%кофе%'
```

В качестве аргументов функции выступают несколько агрегирующих функций:

```sql
SELECT
  AGE(max(birth_date), min(birth_date)) :: varchar as age_diff
FROM
  users
```

**Задание:**

Рассчитайте среднее количество товаров в заказах из таблицы `orders`, которые пользователи оформляли по выходным дням (суббота и воскресенье) в течение всего времени работы сервиса.

Полученное значение округлите до двух знаков после запятой. Колонку с ним назовите `avg_order_size`.

Поле в результирующей таблице: `avg_order_size`

```sql
SELECT
  round(avg(array_length(product_ids, 1)), 2) as avg_order_size
FROM
  orders
WHERE
  DATE_PART('dow', creation_time) = 6
  or DATE_PART('dow', creation_time) = 0
```

**Задание:**

На основе данных в таблице `user_actions` посчитайте количество уникальных пользователей сервиса, количество уникальных заказов, поделите одно на другое и выясните, сколько заказов приходится на одного пользователя.

В результирующей таблице отразите все три значения — поля назовите соответственно `unique_users`, `unique_orders`, `orders_per_user`.

Показатель числа заказов на пользователя округлите до двух знаков после запятой.

Поля в результирующей таблице: `unique_users`, `unique_orders`, `orders_per_user`

```sql
SELECT
  count(DISTINCT user_id) as unique_users,
  count(DISTINCT order_id) as unique_orders,
  round(
    (
      count(DISTINCT order_id) :: decimal / count(DISTINCT user_id)
    ),
    2
  ) as orders_per_user
FROM
  user_actions
```

#### Использование  `FILTER` в агрегирующей функцие:

**Задание:**

Посчитайте, сколько пользователей никогда не отменяли свой заказ. Для этого из общего числа всех уникальных пользователей отнимите число уникальных пользователей, которые хотя бы раз отменяли заказ. Подумайте, какое условие необходимо указать в `FILTER`, чтобы получить корректный результат. Полученный столбец назовите `users_count`.

```sql
SELECT
  (count(DISTINCT user_id)) - (
    count(DISTINCT user_id) FILTER(
      WHERE
        action = 'cancel_order'
    )
  ) as users_count
FROM
  user_actions
```

**Задание:**

Посчитайте общее количество заказов в таблице `orders`, количество заказов с пятью и более товарами и найдите долю заказов с пятью и более товарами в общем количестве заказов.

В результирующей таблице отразите все три значения — поля назовите соответственно `orders`, `large_orders`, `large_orders_share`.

Долю заказов с пятью и более товарами в общем количестве товаров округлите до двух знаков после запятой.

```sql
SELECT
  count (orders) as orders,

  count (orders) FILTER (
    WHERE
      array_length(product_ids, 1) >= 5
  ) as large_orders,

  round(
    (
      count (orders) FILTER (
        WHERE
          array_length(product_ids, 1) >= 5
      )
    ) / (count (orders)) :: decimal,
    2
  ) as large_orders_share
FROM
  orders
```

### Подведём итоги

---

В этом уроке мы:

- Познакомились с ключевым словом `DISTINCT`.

- Разобрались, как работают агрегирующие функции `SUM`, `MIN`, `MAX`, `COUNT`.

- Узнали разницу между `COUNT(*)` и `COUNT(column)`.

- Научились совмещать фильтрацию и агрегацию в одном запросе.

- Поработали с массивами и узнали, что делает функция `array_length`.

- Узнали ещё больше про даты и время и познакомились с функцией `AGE`.  

- Затронули более продвинутую тему — агрегатные выражения с фильтрацией.  

---

Известные нам на текущий момент ключевые слова и порядок их написания в запросе:

```
SELECT     -- перечисление полей результирующей таблицы
FROM       -- указание источника данных
WHERE      -- фильтрация данных
ORDER BY   -- сортировка результирующей таблицы
LIMIT      -- ограничение количества выводимых записей
```
