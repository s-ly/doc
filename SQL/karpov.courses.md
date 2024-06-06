# Курс по SQL

https://lab.karpov.courses/learning/152/module/1762/lesson/18484/53199/251002/

[Login to Redash](https://redash.public.karpov.courses/queries/54778/source)

# Структура базы

![001.jpg](_img_karpov.courses/001.jpg)

# Заметки

```sql
SELECT
  user_id,
  action,
  order_id,
  time
FROM
  user_actions
ORDER BY
  user_id DESC
LIMIT
  10
```

`SELECT` - выбрать колонки

`FROM` - из таблицы

`ORDER` BY - сортировать по

`DESC` - уменьшение

`LIMIT` - ограничение штук

###### 

#### Правильный порядок операторов в запросе выглядит так:

SELECT -- перечисление полей результирующей таблицы
FROM -- указание источника данных
WHERE -- фильтрация данных
GROUP BY -- группировка данных
HAVING -- фильтрация данных после группировки
ORDER BY -- сортировка результирующей таблицы
LIMIT -- ограничение количества выводимых записей

```sql
SELECT
  courier_id,
  action,
  time,
  order_id
FROM
  courier_actions
ORDER BY
  courier_id,
  action,
  time desc
limit
  1000
```

#### Использование псевдонимов:

```sql
SELECT
  name as product_name,
  price as product_price
FROM
  products
ORDER BY
  price desc
limit
  5
```

#### Функция возвращения длины строки:

`LENGTH(name) as name_length` — возвращает товар с самым длинным названием

```sql
SELECT
  name,
  LENGTH(name) as name_length,
  price
FROM
  products
ORDER BY
  name_length desc
limit
  1
```

# 

# Функции разделения и верхнего регистра

`SPLIT_PART(name, ' ', 1)` — разделяет строку **name**, разделитель ' ' и возвращает первую часть.

`UPPER(name) `— переводит в верхний регистр.

`left(name, 3)`  — возвращает левую часть, 3 сивола.

`UPPER(SPLIT_PART(name, ' ', 1))` — составная функция.

```sql
SELECT
  name,
  UPPER(SPLIT_PART(name, ' ', 1)) as first_word,
  left(name, 3) as nemr_left,
  price
FROM
  products
ORDER BY
  name
```

# 

# Изменение тапа данных

`CAST(price as varchar)` — преобразует price в строку.

`price::varchar` — тоже преобразует price в строку.

```sql
SELECT
  name,
  price,
  CAST(price as varchar) as price_char,
  price::varchar
FROM
  products
ORDER BY
  name
```

#### Еще бывают:

`SELECT CAST('100' AS INTEGER)`

Результат:
100

`SELECT '2022-12-31'::DATE`

Результат:
31/12/22

# 

# Соединение строк

```sql
SELECT
  CONCAT(
    'Заказ № ',
    order_id,
    ' создан ',
    date(creation_time)
  ) as order_info
FROM
  orders
limit
  200
```

#### Чтобы извлечь дату из значений в колонке есть несколько способов:

`SELECT DATE(time)`

`SELECT CAST(time AS DATE)`

`SELECT time::DATE`

# 

# Извлечение даты

`DATE_PART('year', birth_date)` — извлечь год.

```sql
SELECT
  courier_id,
  DATE_PART('year', birth_date) as birth_year
FROM
  couriers
ORDER BY
  birth_year desc,
  courier_id asc;
```

#### Ещё примеры:

`SELECT DATE_PART('year', DATE '2022-01-12')`

Результат: 2022.00

`SELECT DATE_PART('month', DATE '2022-01-12')`

Результат: 1.00

`SELECT DATE_PART('day', DATE '2022-01-12')`

Результат: 12.00

`SELECT DATE_PART('hour', TIMESTAMP '2022-01-12 20:31:05')`

Результат: 20.00

`SELECT DATE_PART('minute', TIMESTAMP '2022-01-12 20:31:05')`

Результат: 31.00

# 

# Обработка пустых значений

`COALESCE()` — возвращает первое не пустое значение из списка аргументов и прекращает перебор аргументов.

`SELECT COALESCE(NULL, 'I am not NULL' , 'karpov.courses')`

Результат: I am not NULL

Здесь, в начале извлекаем год, потом переводим его в строку, а уже потом смотрим, пустой он или есть значение. Если пустой, позвращаем строку **unknown**:

```sql
SELECT
  courier_id,
  coalesce(
    CAST(DATE_PART('year', birth_date) as varchar),
    'unknown'
  ) as birth_year
FROM
  couriers
ORDER BY
  birth_year desc,
  courier_id asc;
```

# 

# Арифметические действия

Для работы с числовыми значениями в PostgreSQL доступны разные арифметические операторы, например:

| Оператор | Описание             | Пример | Результат |
| -------- | -------------------- | ------ | --------- |
| +        | Сложение             | 2 + 3  | 5         |
| -        | Вычитание            | 2 - 3  | -1        |
| *        | Умножение            | 2 * 3  | 6         |
| /        | Деление              | 4 / 2  | 2         |
| %        | Остаток от деления   | 5 % 4  | 1         |
| ^        | Возведение в степень | 2 ^ 3  | 8         |

`price * 1.05 as new_price` — увеличение цены на 5%

```sql
SELECT
  product_id,
  name,
  price as old_price,
  price * 1.05 as new_price
FROM
  products
ORDER BY
  new_price desc,
  product_id
```

# 

# Округление

`ROUND(100.5454, 2)` — принимает число и кол-во знков после запятой.

`SELECT ROUND(100.5454, 2)`

Результат: 100.55

`SELECT ROUND(100.551, 1)`

Результат: 100.6

`SELECT ROUND(100.5511)`

Результат: 101.0

```sql
SELECT
  product_id,
  name,
  price as old_price,
  round(price * 1.05, 1) as new_price
FROM
  products
ORDER BY
  new_price desc,
  product_id
```

# 

# Условныен операторы

#### Схема:

```sql
SELECT name,
       CASE 
       WHEN name='свинина' OR name='баранина' OR name='курица' THEN 'мясо'
       WHEN name='треска' OR name='форель' OR name='окунь' THEN 'рыба'
       ELSE 'другое'
       END AS сategory
FROM table
```

#### Описание:

1. На каждом этапе WHEN – THEN вычисляется некоторое логическое выражение logical_expression, стоящее после `WHEN`. Если оно оказывается истинным (`TRUE`), то в качестве результата оператор возвращает выражение expression, стоящее после `THEN`, и заканчивает свою работу.

2. Если выражение оказывается ложным (`FALSE`), то оператор продолжает работу и проверяет следующее условие.  

3. Если ни одно из условий не проходит проверку на истинность, то возвращается выражение, указанное после `ELSE`. При этом `ELSE` указывать не обязательно — если его не указать, то вернётся пустое значение `NULL` (в случае, если все проверки после `WHEN` оказались ложными).  

4. В конце обязательно указывается ключевое слово `END`, которое говорит об окончании конструкции `CASE`. Также после `END` новому расчётному полю с помощью `AS` можно присвоить некоторое имя, но это делать не обязательно. Обязательными являются только ключевые слова `CASE`,  `WHEN`,  `THEN` и `END`.

К операторам сравнения относятся:

- `=` («равно»)
- `<>` или `!=` («не равно»)
- `<` («меньше»)
- `>` («больше»)
- `<=` («меньше или равно»)
- `>=` («больше или равно»)

---

Результатом работы операторов сравнения могут быть три состояния:

- `TRUE` («истина»)

- `FALSE` («ложь»)  

- `NULL` («неопределённое состояние» — когда одно из сравниваемых значений `NULL`)

---

С этими тремя состояниями можно проводить следующие логические операции:

- `AND` («И»)
- `OR` («ИЛИ»)
- `NOT` («НЕ»)

Результатом этих логических операций также могут быть три вышеуказанных логических состояния (`TRUE`, `FALSE` или `NULL`):

| a     | b     | a AND b | a OR b |
| ----- | ----- | ------- | ------ |
| TRUE  | TRUE  | TRUE    | TRUE   |
| TRUE  | FALSE | FALSE   | TRUE   |
| TRUE  | NULL  | NULL    | TRUE   |
| FALSE | FALSE | FALSE   | FALSE  |
| FALSE | NULL  | FALSE   | NULL   |
| NULL  | NULL  | NULL    | NULL   |

---

| a     | NOT a |
| ----- | ----- |
| TRUE  | FALSE |
| FALSE | TRUE  |
| NULL  | NULL  |

---

Также важно понимать, что существуют приоритеты выполнения операций:

1. умножение и деление (`*` и `/`)  

2. сложение и вычитание (`+` и `-`)

3. операторы сравнения (`=`, `!=`, `>`, `<`, `>=`, `<=`)

4. `NOT`

5. `AND`

6. `OR`

#### Пример:

Повышаю цену на товары, которые дороже 100 руб, но кроме икры:

```sql
SELECT
  product_id,
  name,
  price as old_price,
  case
    when price > 100
    and name != 'икра' then price * 1.05
    else price
  end as new_price
FROM
  products
ORDER BY
  new_price desc,
  product_id
```

# 

# Пример из жизни

**Задание:**

Вычислите НДС каждого товара в таблице `products` и рассчитайте цену без учёта НДС. Выведите всю информацию о товарах, включая сумму налога и цену без его учёта. Колонки с суммой налога и ценой без НДС назовите соответственно `tax` и `price_before_tax`. Округлите значения в этих колонках до двух знаков после запятой.

Результат отсортируйте сначала по убыванию цены товара без учёта НДС, затем по возрастанию id товара.

Поля в результирующей таблице: `product_id`, `name`, `price`, `tax`, `price_before_tax`

---

**Пояснение:**

Так как НДС уже включён в текущую цену, налог считаем следующим образом: делим цену на 120% и умножаем на 20%.

**Решение**:

```sql
SELECT
  product_id,
  name,
  price,
  round((price / 120) * 20, 2) as tax,
  round(price - ((price / 120) * 20), 2) as price_before_tax
FROM
  products
ORDER BY
  price_before_tax desc,
  product_id
```

# 

# Фильтрация данных

Порядок записи известных нам на текущий момент ключевых слов выглядит так:

1. `SELECT`
2. `FROM`
3. `WHERE`
4. `ORDER BY`
5. `LIMIT`

Снова обратим внимание, что порядок их выполнения отличается от того, в какой последовательности они указываются в запросе:

1. Сначала выполняется оператор `FROM` — происходит выбор нужной таблицы.

2. Далее `WHERE` — отфильтровываются строки, соответствующие условию.  

3. Затем `SELECT` — отбираются указанные столбцы и применяются функции.

4. Потом `ORDER BY` — производится сортировка результирующей таблицы.

5. И в самом конце `LIMIT` — ограничивается количество выводимых записей.

Иными словами, в результате выполнения запроса сначала происходит подготовка таблицы к работе, а уже затем над ней выполняются разные операции.

```sql
SELECT
  product_id,
  name,
  price
FROM
  products
WHERE
  price <= 100
ORDER BY
  product_id
```

# 

# Фильтроват текст

```sql
SELECT
  user_id
FROM
  users
WHERE
  sex = 'female'
ORDER BY
  user_id
limit
  1000
```

# 

# Фильтрации даты и время

Важно убедиться, что в колонке, по которой будут фильтроваться данные, находятся именно даты или отметки времени, а не данные строкового типа, внешне похожие на даты. Если в колонке окажутся строки, то как таковой ошибки не произойдёт, однако результат будет сильно отличаться от ожидаемого.

Также в операциях сравнения дат важно учитывать, что дата всегда интерпретируется как полночь (начало дня), т.е. '2022-12-31' в действительности означает '2022-12-31 00:00:00'.

```sql
SELECT
  user_id,
  order_id,
  time
FROM
  user_actions
WHERE
  action = 'create_order'
  and time > '2022-09-06 00:00:00'
ORDER BY
  order_id
```

# 

# Расчётные колонки в WHERE

Использовать в блоке `WHERE` алиасы, присвоенные новым расчётным колонкам, нельзя:

```sql
-- Этот запрос сработает:
SELECT column_1, column_2, 
       column_1 / column_2 AS share
FROM table
WHERE column_1 / column_2 > 50

-- Этот запрос вернёт ошибку:
SELECT column_1, column_2, 
       column_1 / column_2 AS share
FROM table
WHERE share > 50
```

```sql
SELECT
  product_id,
  name,
  price as old_price,
  price * 0.8 as new_price
FROM
  products
WHERE
  (price * 0.8) > 100
ORDER BY
  product_id
```

# 

# Функции в WHERE

```sql
SELECT
  product_id,
  name
FROM
  products
WHERE
  SPLIT_PART(name, ' ', 1) = 'чай'
  OR LENGTH(name) = 5
ORDER BY
  product_id
```

# 

# Фильтрация по тексту

Для фильтрации по колонкам с текстовыми значениями - `LIKE`. 

#### Символы-шаблоны:

Знак `_` подчёркивание — подменяет любой одиночный символ, 

знак `%` процента — любую (в том числе и пустую) последовательность символов

#### Примеры:

```sql
SELECT 'karpov.courses' LIKE 'karpov%'
Результат: true

SELECT 'karpov.courses' LIKE 'karpov_'
Результат: false

SELECT 'karpov.courses' LIKE '%karpov%'
Результат: true

SELECT 'karpov.courses' LIKE '_karpov%'
Результат: false

SELECT 'karpov.courses' LIKE '%.%'
Результат: true

SELECT 'karpov.courses' LIKE '_._'
Результат: false

SELECT 'karpov.courses' LIKE 'Karpov%'
Результат: false
```

Ототбрать из таблицы `products` все товары, содержащие в своём названии последовательность символов «чай» (без кавычек):

```sql
SELECT
  product_id,
  name
FROM
  products
WHERE
  name LIKE '%чай%'
ORDER BY
  product_id
```

Выбрать из таблицы `products` id и наименования только тех товаров, названия которых начинаются на букву «с» и содержат только одно слово:

```sql
SELECT
  product_id,
  name
FROM
  products
WHERE
  name NOT LIKE '% %'
  and name LIKE 'с%'
ORDER BY
  product_id
```

# 

# Добавить не существующий столбец

Чтобы указать столбец с одним значением для всех записей (discount), достаточно просто ввести нужное значение и дать столбцу какое-то название.

```sql
SELECT
  product_id,
  name,
  price,
  '25%' as discount,
  price * 0.75 as new_price
FROM
  products
WHERE
  name LIKE '%чай%'
  and name != 'чайный гриб'
  and price > 60
ORDER BY
  product_id
```

# 

# Операторы IN и BETWEEN

`IN` — входит ли значение в этот список

`BETWEEN` — Между. Входят ли в интервал. При этом границы интервала включаются

```sql
SELECT column_1, column_2
FROM table
WHERE column_1 IN ('product_1', 'product_2', 'product_3')
```

```sql
SELECT column_1, column_2
FROM table
WHERE column_2 BETWEEN 5 AND 10
```

#### Дата и время.

Дата интерпретируется как полночь (начало дня), т.е. '2022-12-31' в действительности означает '2022-12-31 00:00:00'. Поэтому в интервал не попадут записи позже полуночи '2022-12-31', т.е. этот день практически не будет учтён в интервале:

```sql
SELECT column_1, column_2, column_3
FROM table
WHERE column_3 BETWEEN '2022-11-20' AND '2022-12-31'
```

Для получения обратного результата в сочетании с операторами `IN` и `BETWEEN` можно использовать оператор `NOT`.

```sql
SELECT column_1, column_2
FROM table
WHERE column_1 NOT IN ('product_1', 'product_2', 'product_3')

SELECT column_1, column_2
FROM table
WHERE column_2 NOT BETWEEN 5 AND 10
```

#### Пример:

Из таблицы `user_actions` выведите всю информацию о действиях пользователей с id 170, 200 и 230 за период с 25 августа по 4 сентября 2022 года включительно. Результат отсортируйте по убыванию id заказа — то есть от самых поздних действий к самым первым.

```sql
SELECT
  user_id,
  order_id,
  action,
  time
FROM
  user_actions
WHERE
  user_id IN (170, 200, 230)
  and time BETWEEN '2022-08-25'
  and '2022-09-04 24:00:00'
ORDER BY
  order_id desc
```

Можно сортировку по датам сделать так:

```sql
WHERE user_id in (170, 200, 230) 
    and time >= '2022-08-25' 
    and time < '2022-09-05'
```

# 

# Проверка на NULL

Примеры:

```sql
Примеры SELECT column_1, column_2
FROM table
WHERE column_1 IS NULL

SELECT column_1, column_2
FROM table
WHERE column_1 IS NOT NULL
```

**Задание:**

Напишите SQL-запрос к таблице `couriers` и выведите всю информацию о курьерах, у которых не указан их день рождения.

```sql
SELECT
  birth_date,
  courier_id,
  sex
FROM
  couriers
WHERE
  birth_date IS NULL
ORDER BY
  courier_id
```

# 

# Ещё задания на фильтрацию

**Задание:**

Определите id и даты рождения 50 самых молодых пользователей мужского пола из таблицы `users`. Не учитывайте тех пользователей, у которых не указана дата рождения.

Поле в результирующей таблице: `user_id`, `birth_date`

```sql
SELECT
  user_id,
  birth_date
FROM
  users
WHERE
  birth_date IS NOT NULL and sex = 'male'
ORDER BY
  birth_date desc
LIMIT
  50
```

**Задание:**

Напишите SQL-запрос к таблице `courier_actions`, чтобы узнать id и время доставки последних 10 заказов, доставленных курьером с id 100.

Поля в результирующей таблице: `order_id`, `time`

```sql
SELECT
  order_id,
  time
FROM
  courier_actions
WHERE
  courier_id = 100
  and action = 'deliver_order'
ORDER BY
  time desc
LIMIT
  10
```

**Задание:**

Из таблицы `user_actions` получите id всех заказов, сделанных пользователями сервиса в августе 2022 года.

Результат отсортируйте по возрастанию id заказа.

Поле в результирующей таблице: `order_id`

```sql
SELECT
  order_id
FROM
  user_actions
WHERE
  action = 'create_order'
  and time BETWEEN '2022-08-01'
  and '2022-08-31 23:59:59'
ORDER BY
  order_id
```

Второй вариант решения:

```sql
SELECT
  order_id
FROM
  user_actions
WHERE
  action = 'create_order'
  and date_part('month', time) = 8
  and date_part('year', time) = 2022
ORDER BY
  order_id
```

**Задание:**

Из таблицы `couriers` отберите id всех курьеров, родившихся в период с 1990 по 1995 год включительно.

Результат отсортируйте по возрастанию id курьера.

Поле в результирующей таблице: `courier_id`

```sql
SELECT
  courier_id
FROM
  couriers
WHERE
  date_part('year', birth_date) BETWEEN 1990
  and 1995
ORDER BY
  courier_id
```

**Задание:**

Из таблицы `user_actions` получите информацию о всех отменах заказов, которые пользователи совершали в течение августа 2022 года по средам с 12:00 до 15:59.

Результат отсортируйте по убыванию id отменённых заказов.

Поля в результирующей таблице: `user_id`, `order_id`, `action`, `time`

**Пояснение:**

Будьте внимательны при работе с датами и временем.

Для решения задачи вам может пригодиться функция `DATE_PART`. Для получения дня недели можно указать аргумент 'dow' («day of week»):

```sql
SELECT DATE_PART('dow', DATE '2022-12-31')

Результат: 6.00
```

В PostgreSQL дни недели считаются с воскресенья (0) до субботы (6).

**Решение:**

```sql
SELECT
  *
FROM
  user_actions
WHERE
  action = 'cancel_order'
  and DATE_PART('year', time) = 2022
  and DATE_PART('month', time) = 8
  and DATE_PART('dow', time) = 3
  and DATE_PART('hour', time) BETWEEN 12
  and 15
ORDER BY
  order_id desc
```

**Задание:**

Как и в [задаче из прошлого урока](https://lab.karpov.courses/learning/152/module/1762/lesson/18484/53190/250931/), вычислите НДС каждого товара в таблице `products` и рассчитайте цену без учёта НДС. Однако теперь примите во внимание, что для товаров из списка налог составляет 10%. Для остальных товаров НДС тот же — 20%.

Выведите всю информацию о товарах, включая сумму налога и цену без его учёта. Колонки с суммой налога и ценой без НДС назовите соответственно `tax` и `price_before_tax`. Округлите значения в этих колонках до двух знаков после запятой.

Результат отсортируйте сначала по убыванию цены товара без учёта НДС, затем по возрастанию id товара.

Поля в результирующей таблице: `product_id`, `name`, `price`, `tax`, `price_before_tax`

```sql
SELECT
  product_id,
  name,
  price,
  CASE
    WHEN name IN (
      'сахар', 'сухарики', 'сушки', 'семечки', 'масло льняное', 'виноград', 'масло оливковое', 'арбуз', 'батон', 'йогурт', 'сливки', 'гречка', 'овсянка', 'макароны', 'баранина', 'апельсины', 'бублики', 'хлеб', 'горох', 'сметана', 'рыба копченая', 'мука', 'шпроты', 'сосиски', 'свинина', 'рис', 'масло кунжутное', 'сгущенка', 'ананас', 'говядина', 'соль', 'рыба вяленая', 'масло подсолнечное', 'яблоки', 'груши', 'лепешка', 'молоко', 'курица', 'лаваш', 'вафли', 'мандарины'
    ) THEN round(price - ((price / 110) * 10), 2)
    ELSE round(price - ((price / 120) * 20), 2)
  END as price_before_tax,
  CASE
    WHEN name IN (
      'сахар', 'сухарики', 'сушки', 'семечки', 'масло льняное', 'виноград', 'масло оливковое', 'арбуз', 'батон', 'йогурт', 'сливки', 'гречка', 'овсянка', 'макароны', 'баранина', 'апельсины', 'бублики', 'хлеб', 'горох', 'сметана', 'рыба копченая', 'мука', 'шпроты', 'сосиски', 'свинина', 'рис', 'масло кунжутное', 'сгущенка', 'ананас', 'говядина', 'соль', 'рыба вяленая', 'масло подсолнечное', 'яблоки', 'груши', 'лепешка', 'молоко', 'курица', 'лаваш', 'вафли', 'мандарины'
    ) THEN round((price / 110) * 10, 2)
    ELSE round((price / 120) * 20, 2)
  END as tax
FROM
  products
ORDER BY
  price_before_tax desc,
  product_id
```
