# ГРУППИРОВКА ДАННЫХ SQL

#### Логика групировки:

1. Сначала в таблице определяются строки, в которых в указанном в `GROUP BY` столбце есть одинаковые значения.

2. Далее по этим значениям записи объединяются в группы, причём в группе может быть даже одна запись.  

3. После этого с помощью агрегирующих функций над элементами этих групп, как правило, проводятся определённые операции. Например, с помощью `SUM` вычисляется сумма значений в каком-либо столбце в каждой группе.

4. Затем в результирующий запрос попадает по одной уникальной записи из столбца, по которому осуществлялась группировка. При этом напротив каждого такого значения отражается результат агрегации по соответствующей группе, если агрегация проводилась.

```sql
SELECT column_1, SUM(column_2) AS sum
FROM table
GROUP BY column_1
```

Таким образом, порядок записи ключевых слов выглядит так:

1. `SELECT`
2. `FROM`
3. `WHERE`
4. `GROUP BY`
5. `ORDER BY`
6. `LIMIT`

В то же время порядок выполнения операторов в запросе следующий:

1. Сначала выполняется оператор `FROM` — происходит выбор нужной таблицы.
2. Далее `WHERE` — отфильтровываются строки, соответствующие условию.
3. Потом `GROUP BY` — строки объединяются в группы и производится агрегация.
4. Затем `SELECT` — отбираются указанные столбцы.
5. Потом `ORDER BY` — производится сортировка результирующей таблицы.
6. И в самом конце `LIMIT` — ограничивается количество выводимых записей.

#### 

#### Уточнения:

Группировку можно делать по нескольким колонкам:

```sql
SELECT column_1, column_2, SUM(column_3) AS sum
FROM table
GROUP BY column_1, column_2
```

К группам можно применять несколько агрегирующих функций:

```sql
SELECT column_1, SUM(column_2) AS sum, AVG(column_3) AS avg
FROM table
GROUP BY column_1
```

Агрегирующую функцию можно применить к колонке, по которой осуществляется группировка:

```sql
SELECT column_1, COUNT(column_1) AS count
FROM table
GROUP BY column_1
```

После группировки, к результатам агрегирующих функций можно применять другие функции и проводить арифметические операции:

```sql
SELECT column_1, ROUND(SUM(column_2), 2) AS rounded_sum
FROM table
GROUP BY column_1

SELECT column_1, SUM(column_2)::DECIMAL / SUM(column_3) * 100 AS share
FROM table
GROUP BY column_1
```

Агрегацию не обязательно проводить по уже имеющимся колонкам — можно сначала применить некоторую функцию в колонке из исходной таблицы, а затем провести агрегацию по новой колонке:

```sql
SELECT column_1, SUM(some_function(column_2)) AS sum_over_function
FROM table
GROUP BY column_1
```

Группировку можно делать сразу по новым полям, посчитанным в `SELECT`:

```sql
SELECT DATE(column_1) AS date, SUM(column_2) AS sum
FROM table
GROUP BY DATE(column_1)
```

Делать агрегацию после группировки не обязательно. Если не указать агрегирующую функцию, то запрос вернёт уникальные значения в столбце (или уникальные комбинации значений в столбцах):

```sql
SELECT user_id
FROM user_actions
GROUP BY user_id
```

При использовании группировки колонки, указанные в `SELECT`, должны находиться и в `GROUP BY`, если они не используются в агрегирующих функциях:

```sql
SELECT column_1, column_2, SUM(column_3) AS sum
FROM table
GROUP BY column_1, column_2
```

Если указать что-то в `GROUP BY`, то это совсем не обязательно указывать в `SELECT`.

```sql
SELECT SUM(column_2) AS sum
FROM table
GROUP BY column_1
```

Группировка всегда выполняется после фильтрации, т.е. сначала выполняются инструкции в `WHERE` и только потом данные группируются с помощью `GROUP BY`:

```sql
SELECT column_1, SUM(column_2) AS sum
FROM table
WHERE column_3 > 0
GROUP BY column_1
```

**Задание:**

С помощью оператора `GROUP BY` посчитайте количество курьеров мужского и женского пола в таблице `couriers`.

Новую колонку с числом курьеров назовите `couriers_count`.

Результат отсортируйте по этой колонке по возрастанию.

Поля в результирующей таблице: `sex`, `couriers_count`

```sql
SELECT
  sex,
  count(sex) as couriers_count
FROM
  couriers
GROUP BY
  sex
ORDER BY
  couriers_count
```

**Задание:**

Посчитайте количество созданных и отменённых заказов в таблице `user_actions`.

Новую колонку с числом заказов назовите `orders_count`.

Результат отсортируйте по числу заказов по возрастанию.

Поля в результирующей таблице: `action`, `orders_count`

```sql
SELECT
  action, count(action) as orders_count
FROM
  user_actions
GROUP BY
  action
ORDER BY
  orders_count
```

Усечения дат и времени

Функция `DATE_TRUNC` используется для усечения дат и времени, т.е. она работает аналогично округлению `ROUND`, только для типов данных `TIMESTAMP` и `INTERVAL`.

```sql
SELECT DATE_TRUNC('month', TIMESTAMP '2022-01-12 08:55:30')
Результат: 01/01/22 00:00

SELECT DATE_TRUNC('day', TIMESTAMP '2022-01-12 08:55:30')
Результат: 12/01/22 00:00    

SELECT DATE_TRUNC('hour', TIMESTAMP '2022-01-12 08:55:30')
Результат: 12/01/22 08:00    
```

**Задание:**

Используя группировку и функцию `DATE_TRUNC`, приведите все даты к началу месяца и посчитайте, сколько заказов было сделано в каждом из них.

Расчёты проведите по таблице `orders`. Колонку с усечённой датой назовите `month`, колонку с количеством заказов — `orders_count`.

Результат отсортируйте по месяцам — по возрастанию.

Поля в результирующей таблице: `month`, `orders_count`

```sql
SELECT
  DATE_TRUNC('month', creation_time) as month,
  count(DATE_TRUNC('month', creation_time)) as orders_count
FROM
  orders
GROUP BY
  month
ORDER BY
  month
```

**Задание:**

Используя группировку и функцию `DATE_TRUNC`, приведите все даты к началу месяца и посчитайте, сколько заказов было сделано и сколько было отменено в каждом из них.

В этот раз расчёты проведите по таблице `user_actions`. Колонку с усечённой датой назовите `month`, колонку с количеством заказов — `orders_count`.

Результат отсортируйте сначала по месяцам — по возрастанию, затем по типу действия — тоже по возрастанию.

Поля в результирующей таблице: `month`, `action`, `orders_count`

```sql
SELECT
  DATE_TRUNC('month', time) as month,
  count(action) as orders_count,
  action
FROM
  user_actions
GROUP BY
  month,
  action
ORDER BY
  month,
  action
```

Агрегацию не обязательно проводить по уже имеющимся колонкам — колонки можно «создавать» в рамках того же запроса, в котором происходит группировка.

**Задание:**

По данным в таблице `users` посчитайте максимальный порядковый номер месяца среди всех порядковых номеров месяцев рождения пользователей сервиса. С помощью группировки проведите расчёты отдельно в двух группах — для пользователей мужского и женского пола.

Новую колонку с максимальным номером месяца рождения в группах назовите `max_month`. Преобразуйте значения в новой колонке в формат `INTEGER`, чтобы порядковый номер был выражен целым числом.

Результат отсортируйте по колонке с полом пользователей.

Поля в результирующей таблице: `sex`, `max_month`

```sql
SELECT
  sex,
  max(DATE_PART('month', birth_date) :: integer) as max_month
FROM
  users
GROUP BY
  sex
```

**Задание:**

По данным в таблице `users` посчитайте порядковый номер месяца рождения самого молодого пользователя сервиса. С помощью группировки проведите расчёты отдельно в двух группах — для пользователей мужского и женского пола.

Новую колонку с максимальным номером месяца рождения в группах назовите `max_month`. Преобразуйте значения в новой колонке в формат `INTEGER`, чтобы порядковый номер был выражен целым числом.

Результат отсортируйте по колонке с полом пользователей.

Поля в результирующей таблице: `sex`, `max_month`

```sql
SELECT
  sex,
  DATE_PART('month', max(birth_date)) :: integer as max_month
FROM
  users
GROUP BY
  sex
```

**Задание:**

Посчитайте максимальный возраст пользователей мужского и женского пола в таблице `users`. Возраст измерьте числом полных лет.

Новую колонку с возрастом назовите `max_age`. Преобразуйте значения в новой колонке в формат `INTEGER`, чтобы возраст был выражен целым числом.

Результат отсортируйте по новой колонке по возрастанию возраста.

Поля в результирующей таблице: `sex`, `max_age`

```sql
SELECT
  sex,
  DATE_PART('year', AGE(current_date, min(birth_date))) :: integer as max_age
FROM
  users
GROUP BY
  sex
ORDER BY
  max_age
```

**Задание:**

Разбейте пользователей из таблицы `users` на группы по возрасту (возраст по-прежнему измеряем числом полных лет) и посчитайте количество пользователей каждого возраста.

Колонку с возрастом назовите `age`, а колонку с числом пользователей — `users_count`. Преобразуйте значения в колонке с возрастом в формат `INTEGER`, чтобы возраст был выражен целым числом.

Результат отсортируйте по колонке с возрастом по возрастанию.

Поля в результирующей таблице: `age`, `users_count`

```sql
SELECT date_part('year', age(birth_date))::integer as age,
       count(user_id) as users_count
FROM   users
GROUP BY age
ORDER BY age
```

**Задание:**

Вновь разбейте пользователей из таблицы `users` на группы по возрасту (возраст по-прежнему измеряем количеством полных лет), только теперь добавьте в группировку ещё и пол пользователя. Затем посчитайте количество пользователей в каждой половозрастной группе.

Все `NULL` значения в колонке `birth_date` заранее отфильтруйте с помощью `WHERE`.

Колонку с возрастом назовите `age`, а колонку с числом пользователей — `users_count`, имя колонки с полом оставьте без изменений. Преобразуйте значения в колонке с возрастом в формат `INTEGER`, чтобы возраст был выражен целым числом.

Отсортируйте полученную таблицу сначала по колонке с возрастом по возрастанию, затем по колонке с полом — тоже по возрастанию.

Поля в результирующей таблице: `age`, `sex`, `users_count`

```sql
SELECT
  date_part('year', age(birth_date)) :: integer as age,
  sex,
  count(user_id) as users_count
FROM
  users
WHERE
  birth_date IS NOT NULL
GROUP BY
  age,
  sex
ORDER BY
  age,
  sex
```

**Задание:**

Посчитайте количество товаров в каждом заказе, примените к этим значениям группировку и рассчитайте количество заказов в каждой группе за неделю с 29 августа по 4 сентября 2022 года включительно. Для расчётов используйте данные из таблицы `orders`.

Выведите две колонки: размер заказа и число заказов такого размера за указанный период. Колонки назовите соответственно `order_size` и `orders_count`.

Результат отсортируйте по возрастанию размера заказа.

Поля в результирующей таблице: `order_size`, `orders_count`

```sql
SELECT
  array_length(product_ids, 1) as order_size,
  count (product_ids) as orders_count
FROM
  orders
WHERE
  creation_time >= '2022-08-29'
  and creation_time <= '2022-09-04 24:00:00'
GROUP BY
  order_size
ORDER BY
  order_size
```

# 

# Наложить фильтрацию поверх группировки — HAVING

`AVING` — выполняет фильтрацию поверх уже сгруппированных данных.

В `HAVING` нельзя указывать алиасы полей с результатом агрегации из блока `SELECT`. Так-как оператор `SELECT` выполняется после оператора `HAVING`. 

Список ключевых слов в запросе:

1. `SELECT`
2. `FROM`
3. `WHERE`
4. `GROUP BY`
5. `HAVING`
6. `ORDER BY`
7. `LIMIT`

Порядок их выполнения в запросе следующий:

1. Сначала выполняется оператор `FROM` — происходит выбор нужной таблицы.
2. Далее `WHERE` — отфильтровываются строки, соответствующие условию.
3. Потом `GROUP BY` — строки объединяются в группы и производится агрегация.
4. Затем `HAVING` — отфильтровываются группы, соответствующие условию.
5. После этого `SELECT` — отбираются указанные столбцы.
6. Потом `ORDER BY` — производится сортировка результирующей таблицы.
7. И в самом конце `LIMIT` — ограничивается количество выводимых записей.

Для получения наименования дня недели из колонки с датой или отметкой времени можно использовать, например, функцию `DATE_PART` с параметром 'isodow' либо функцию `TO_CHAR` с параметром 'Dy':

```sql
SELECT TO_CHAR(TIMESTAMP '2022-08-29', 'Dy')
Результат: Mon
```

Многие системы управления базами данных (СУБД) используют нулевую позицию для обозначения воскресенья. Это означает, что воскресенье ("Sun") будет иметь значение 0, а суббота ("Sat") — значение 6.

```
0. Sun - Воскресенье
1. Mon - Понедельник
2. Tue - Вторник
3. Wed - Среда
4. Thu - Четверг
5. Fri - Пятница
6. Sat - Суббота
```

**Задание:**

Посчитайте количество товаров в каждом заказе, примените к этим значениям группировку и рассчитайте количество заказов в каждой группе. Учитывайте только заказы, оформленные по будням. В результат включите только те размеры заказов, общее число которых превышает 2000. Для расчётов используйте данные из таблицы `orders`.

Выведите две колонки: размер заказа и число заказов такого размера. Колонки назовите соответственно `order_size` и `orders_count`.

Результат отсортируйте по возрастанию размера заказа.

Поля в результирующей таблице: `order_size`, `orders_count`

```sql
SELECT
  array_length(product_ids, 1) as order_size,
  count(order_id) as orders_count
FROM
  orders
WHERE
  to_char(creation_time, 'Dy') not in ('Sat', 'Sun')
GROUP BY
  order_size
having
  count(order_id) > 2000
ORDER BY
  order_size
```

**Задание:**

По данным из таблицы `user_actions` определите пять пользователей, сделавших в августе 2022 года наибольшее количество заказов.

Выведите две колонки — id пользователей и число оформленных ими заказов. Колонку с числом оформленных заказов назовите `created_orders`.

Результат отсортируйте сначала по убыванию числа заказов, сделанных пятью пользователями, затем по возрастанию id этих пользователей.

Поля в результирующей таблице: `user_id`, `created_orders`

```sql
SELECT
  user_id,
  count (action) as created_orders
FROM
  user_actions
WHERE
  DATE_PART('month', time) = 8
  and action = 'create_order'
GROUP BY
  user_id
ORDER BY
  created_orders desc,
  user_id
limit
  5
```

**Задание:**

А теперь по данным таблицы `courier_actions` определите курьеров, которые в сентябре 2022 года доставили только по одному заказу.

В этот раз выведите всего одну колонку с id курьеров. Колонку с числом заказов в результат включать не нужно.

Результат отсортируйте по возрастанию id курьера.

Поле в результирующей таблице: `courier_id`

```sql
SELECT courier_id
FROM   courier_actions
WHERE  action = 'deliver_order'
   and date_part('month', time) = 9
   and date_part('year', time) = 2022
GROUP BY courier_id having count(distinct order_id) = 1
ORDER BY courier_id
```

**Задание:**

Из таблицы `user_actions` отберите пользователей, у которых последний заказ был создан до 8 сентября 2022 года.

Выведите только их id, дату создания заказа выводить не нужно.

Результат отсортируйте по возрастанию id пользователя.

Поле в результирующей таблице: `user_id`

```sql
SELECT
  user_id
FROM
  user_actions
WHERE
  action = 'create_order'
GROUP BY
  user_id
having
  max(time) < '2022-09-08'
ORDER BY
  user_id
```

**Задание:**

Разбейте заказы из таблицы `orders` на 3 группы в зависимости от количества товаров, попавших в заказ:

- Малый (от 1 до 3 товаров);
- Средний (от 4 до 6 товаров);
- Большой (7 и более товаров).

Посчитайте число заказов, попавших в каждую группу. Группы назовите соответственно `«Малый»`, `«Средний»`, `«Большой»` (без кавычек).

Выведите наименования групп и число товаров в них. Колонку с наименованием групп назовите `order_size`, а колонку с числом заказов — `orders_count`.

Отсортируйте полученную таблицу по колонке с числом заказов по возрастанию.

Поля в результирующей таблице: `order_size`, `orders_count`

```sql
SELECT case when array_length(product_ids, 1) >= 7 then 'Большой'
            when array_length(product_ids, 1) >= 4 then 'Средний'
            else 'Малый' end as order_size,
       count(order_id) as orders_count
FROM   orders
GROUP BY order_size
ORDER BY orders_count
```

**Задание:**

Разбейте пользователей из таблицы `users` на 4 возрастные группы:

- от 18 до 24 лет;
- от 25 до 29 лет;
- от 30 до 35 лет;
- старше 36.

Посчитайте число пользователей, попавших в каждую возрастную группу. Группы назовите соответственно `«18-24»`, `«25-29»`, `«30-35»`, `«36+»` (без кавычек).

В расчётах не учитывайте пользователей, у которых не указана дата рождения. Как и в прошлых задачах, в качестве возраста учитывайте число полных лет.

Выведите наименования групп и число пользователей в них. Колонку с наименованием групп назовите `group_age`, а колонку с числом пользователей — `users_count`.

Отсортируйте полученную таблицу по колонке с наименованием групп по возрастанию.

Поля в результирующей таблице: `group_age`, `users_count`

```sql
SELECT case when date_part('year', age(birth_date)) between 18 and
                 24 then '18-24'
            when date_part('year', age(birth_date)) between 25 and
                 29 then '25-29'
            when date_part('year', age(birth_date)) between 30 and
                 35 then '30-35'
            when date_part('year', age(birth_date)) >= 36 then '36+' end as group_age,
       count(user_id) as users_count
FROM   users
WHERE  birth_date is not null
GROUP BY group_age
ORDER BY group_age
```

**Задание:**

По данным из таблицы `orders` рассчитайте средний размер заказа по выходным и будням.

Группу с выходными днями (суббота и воскресенье) назовите «weekend», а группу с будними днями (с понедельника по пятницу) — «weekdays» (без кавычек).

В результат включите две колонки: колонку с группами назовите `week_part`, а колонку со средним размером заказа — `avg_order_size`. 

Средний размер заказа округлите до двух знаков после запятой.

Результат отсортируйте по колонке со средним размером заказа — по возрастанию.

Поля в результирующей таблице: `week_part`, `avg_order_size`

```sql
SELECT
  CASE
    WHEN to_char(creation_time, 'Dy') not in ('Sat', 'Sun') THEN 'weekdays'
    else 'weekend'
  END as week_part,
  round(avg(array_length(product_ids, 1)), 2) as avg_order_size
FROM
  orders
GROUP BY
  week_part
ORDER BY
  avg_order_size
```

**Задание:**

Для каждого пользователя в таблице `user_actions` посчитайте общее количество оформленных заказов и долю отменённых заказов.

Новые колонки назовите соответственно `orders_count` и `cancel_rate`. Колонку с долей отменённых заказов округлите до двух знаков после запятой.

В результат включите только тех пользователей, которые оформили больше трёх заказов и у которых показатель cancel_rate составляет не менее 0.5.

Результат отсортируйте по возрастанию id пользователя.

Поля в результирующей таблице: `user_id`, `orders_count`, `cancel_rate`

```sql
SELECT
  user_id,
  count(order_id) FILTER(
    WHERE
      action = 'create_order'
  ) as orders_count,
  round(
    (
      (
        (
          (
            count(order_id) FILTER(
              WHERE
                action = 'cancel_order'
            )
          ) :: decimal
        ) * 1
      ) / (
        count(order_id) FILTER(
          WHERE
            action = 'create_order'
        )
      )
    ),
    2
  ) as cancel_rate
FROM
  user_actions
GROUP BY
  user_id
having
  count(order_id) FILTER(
    WHERE
      action = 'create_order'
  ) > 3
  and (
    round(
      (
        (
          (
            (
              count(order_id) FILTER(
                WHERE
                  action = 'cancel_order'
              )
            ) :: decimal
          ) * 1
        ) / (
          count(order_id) FILTER(
            WHERE
              action = 'create_order'
          )
        )
      ),
      2
    )
  ) >= 0.5
ORDER BY
  user_id
```

Тоже самое, но более правильно спроектированно:

```sql
SELECT
  user_id,
  round(
    count(distinct order_id) filter (
      WHERE
        action = 'cancel_order'
    ) :: decimal / count(distinct order_id),
    2
  ) as cancel_rate,
  count(distinct order_id) as orders_count
FROM
  user_actions
GROUP BY
  user_id
having
  round(
    count(distinct order_id) filter (
      WHERE
        action = 'cancel_order'
    ) :: decimal / count(distinct order_id),
    2
  ) >= 0.5
  and count(distinct order_id) > 3
ORDER BY
  user_id
```

**Задание:**

Для каждого дня недели в таблице `user_actions` посчитайте:

1. Общее количество оформленных заказов.
2. Общее количество отменённых заказов.
3. Общее количество неотменённых заказов (т.е. доставленных).
4. Долю неотменённых заказов в общем числе заказов (success rate).

Новые колонки назовите соответственно `created_orders`, `canceled_orders`, `actual_orders` и `success_rate`. Колонку с долей неотменённых заказов округлите до трёх знаков после запятой.

Все расчёты проводите за период с 24 августа по 6 сентября 2022 года включительно, чтобы во временной интервал попало равное количество разных дней недели.

Группы сформируйте следующим образом: выделите день недели из даты с помощью функции `to_char` с параметром 'Dy', также выделите порядковый номер дня недели с помощью функции `DATE_PART` с параметром 'isodow'. Далее сгруппируйте данные по двум полям и проведите все необходимые расчёты.

В результате должна получиться группировка по двум колонкам: с порядковым номером дней недели и их сокращёнными наименованиями.

Результат отсортируйте по возрастанию порядкового номера дня недели.

Поля в результирующей таблице: `weekday_number`, `weekday`, `created_orders`, `canceled_orders`, `actual_orders`, `success_rate`



Моё решение:

```sql
SELECT
  CASE
    WHEN DATE_PART('isodow', time) = 1 THEN '1'
    WHEN DATE_PART('isodow', time) = 2 THEN '2'
    WHEN DATE_PART('isodow', time) = 3 THEN '3'
    WHEN DATE_PART('isodow', time) = 4 THEN '4'
    WHEN DATE_PART('isodow', time) = 5 THEN '5'
    WHEN DATE_PART('isodow', time) = 6 THEN '6'
    WHEN DATE_PART('isodow', time) = 7 THEN '7'
  END as weekday_number,
  CASE
    WHEN to_char(time, 'Dy') = 'Mon' THEN 'Mon'
    WHEN to_char(time, 'Dy') = 'Tue' THEN 'Tue'
    WHEN to_char(time, 'Dy') = 'Wed' THEN 'Wed'
    WHEN to_char(time, 'Dy') = 'Thu' THEN 'Thu'
    WHEN to_char(time, 'Dy') = 'Fri' THEN 'Fri'
    WHEN to_char(time, 'Dy') = 'Sat' THEN 'Sat'
    WHEN to_char(time, 'Dy') = 'Sun' THEN 'Sun'
  END as weekday,
  count(order_id) filter (
    WHERE
      action = 'create_order'
  ) as created_orders,
  count(order_id) filter (
    WHERE
      action = 'cancel_order'
  ) as canceled_orders,
  (
    count(order_id) filter (
      WHERE
        action = 'create_order'
    )
  ) - (
    count(order_id) filter (
      WHERE
        action = 'cancel_order'
    )
  ) as actual_orders,
  round(
    (
      (
        (
          count(order_id) filter (
            WHERE
              action = 'create_order'
          )
        ) - (
          count(order_id) filter (
            WHERE
              action = 'cancel_order'
          )
        )
      ) / (
        count(order_id) filter (
          WHERE
            action = 'create_order'
        )
      ) :: decimal
    ),
    3
  ) as success_rate
FROM
  user_actions
WHERE
  time >= '2022-08-24'
  and time <= '2022-09-06 24:00:00'
GROUP BY
  weekday_number,
  weekday
ORDER BY
  weekday_number
```

Решение курса:

```sql
SELECT
  date_part('isodow', time) :: int as weekday_number,
  to_char(time, 'Dy') as weekday,
  count(order_id) filter (
    WHERE
      action = 'create_order'
  ) as created_orders,
  count(order_id) filter (
    WHERE
      action = 'cancel_order'
  ) as canceled_orders,
  count(order_id) filter (
    WHERE
      action = 'create_order'
  ) - count(order_id) filter (
    WHERE
      action = 'cancel_order'
  ) as actual_orders,
  round(
    (
      count(order_id) filter (
        WHERE
          action = 'create_order'
      ) - count(order_id) filter (
        WHERE
          action = 'cancel_order'
      )
    ) :: decimal / count(order_id) filter (
      WHERE
        action = 'create_order'
    ),
    3
  ) as success_rate
FROM
  user_actions
WHERE
  time >= '2022-08-24'
  and time < '2022-09-07'
GROUP BY
  weekday_number,
  weekday
ORDER BY
  weekday_number
```

На текущий момент (по курсу) ключевые слова и порядок их написания в запросе:

```
SELECT     -- перечисление полей результирующей таблицы
FROM       -- указание источника данных
WHERE      -- фильтрация данных
GROUP BY   -- группировка данных
HAVING     -- фильтрация данных после группировки
ORDER BY   -- сортировка результирующей таблицы
LIMIT      -- ограничение количества выводимых записей
```


