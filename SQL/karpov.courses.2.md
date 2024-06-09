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
