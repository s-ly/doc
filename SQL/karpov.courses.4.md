# Подзапросы SQL

Подзапрос — это такая же таблица, только временная. Она формируется в процессе выполнения основного запроса и нигде не сохраняется.

**Задание:**

Используя данные из таблицы `user_actions`, рассчитайте среднее число заказов всех пользователей нашего сервиса.

Для этого сначала в подзапросе посчитайте, сколько заказов сделал каждый пользователь, а затем обратитесь к результату подзапроса в блоке `FROM` и уже в основном запросе усредните количество заказов по всем пользователям.

Полученное среднее число заказов всех пользователей округлите до двух знаков после запятой. Колонку с этим значением назовите `orders_avg`.

Поле в результирующей таблице: `orders_avg`

```sql
SELECT
  round(avg(sub_sel), 2) as orders_avg
FROM
  (
    SELECT
      count(order_id) as sub_sel
    FROM
      user_actions
    WHERE
      action = 'create_order'
    GROUP BY
      user_id
  ) as sub_from
```

# Табличные выражения WITH

Табличные выражения — это временные таблицы, существующие только для одного запроса. Их основное предназначение заключается в разбиении сложных запросов на несколько частей.

**Задание:**

Повторите запрос из предыдущего задания, но теперь вместо подзапроса используйте оператор `WITH` и табличное выражение.

Условия задачи те же: используя данные из таблицы `user_actions`, рассчитайте среднее число заказов всех пользователей.

Полученное среднее число заказов округлите до двух знаков после запятой. Колонку с этим значением назовите `orders_avg`.

Поле в результирующей таблице: `orders_avg`

```sql
WITH sub_w AS (
  SELECT
    COUNT(order_id) AS sub_sel
  FROM
    user_actions
  WHERE
    action = 'create_order'
  GROUP BY
    user_id
)
SELECT
  ROUND(AVG(sub_sel), 2) AS orders_avg
FROM
  sub_w;
```

**Задание:**

Выведите из таблицы `products` информацию о всех товарах кроме самого дешёвого.

Результат отсортируйте по убыванию id товара.

Поля в результирующей таблице: `product_id`, `name`, `price`

Моё решение:

```sql
with sub_t as (
  select
    min(price) as min_prise
  from
    products
)
select
  product_id,
  name,
  price
from
  products,
  sub_t
WHERE
  price != min_prise
ORDER BY
  product_id desc
```

Учебное решение:

```sql
SELECT
  product_id,
  name,
  price
FROM
  products
WHERE
  price != (
    SELECT
      min(price)
    FROM
      products
  )
ORDER BY
  product_id desc
```

**Задание:**

Выведите информацию о товарах в таблице `products`, цена на которые превышает среднюю цену всех товаров на 20 рублей и более. Результат отсортируйте по убыванию id товара.

Поля в результирующей таблице: `product_id`, `name`, `price`

```sql
with sub_t as (
  select
    avg(price) as avg_prise
  from
    products
)
select
  product_id,
  name,
  price
from
  products,
  sub_t
WHERE
  price >= (avg_prise + 20)
ORDER BY
  product_id desc
```

# 

# Интрвал времени

Чтобы отложить от даты или прибавить к ней какой-то промежуток времени, можно от текущей даты отнять заданный промежуток `INTERVAL`:

```sql
SELECT NOW() - INTERVAL '1 year 2 months 1 week'

Результат: 10/10/21 19:32
```

`NOW` — позволяет получать текущую дату и время



**Задание:**

Посчитайте количество уникальных клиентов в таблице `user_actions`, сделавших за последнюю неделю хотя бы один заказ.

Полученную колонку с числом клиентов назовите `users_count`. В качестве текущей даты, от которой откладывать неделю, используйте последнюю дату в той же таблице `user_actions`.

Поле в результирующей таблице: `users_count`

Моё решение:

```sql
with sub_t1 as (
  select
    max(time) as max_time
  from
    user_actions
),
sub_t2 as (
  select
    max_time - interval '1 week' as last_week
  from
    sub_t1
)
SELECT
  count (DISTINCT user_id) as users_count
FROM
  sub_t1,
  sub_t2,
  user_actions
where
  action = 'create_order'
  and time BETWEEN last_week
  and max_time
```

Учебное решение:

```sql
SELECT count(distinct user_id) as users_count
FROM   user_actions
WHERE  action = 'create_order'
   and time >= (SELECT max(time)
             FROM   user_actions) - interval '1 week'
```
