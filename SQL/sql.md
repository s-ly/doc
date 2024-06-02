```sql
-- Создать таблицу
CREATE TABLE aircrafts (
aircraft_code CHAR(3) not NULL,
model text not NULL,
range integer not NULL,
check (range > 0),
PRIMARY KEY (aircraft_code)
);
--
-- Удалить таблицу
DROP TABLE aircrafts;
--
-- Вставить в таблицу
INSERT INTO aircrafts(aircraft_code, model, range)
VALUES ('SU9', 'Sukhoi SuperJet-100', 3000),
('773', 'Boeing 777-300', 11100),
('763', 'Boeing 767-300', 7900),
('733', 'Boeing 737-300', 4200),
('320', 'Airbus A320-200', 5700),
('321', 'Airbus A321-200', 5600),
('319', 'Airbus A319-100', 6700),
('CN1', 'Cessna 208 Caravan', 1200),
('CN2', 'Bombardier CRJ-200', 2700);
--
```

```sql
-- Распечатать всё из таблицы
SELECT *
FROM aircrafts;
--
-- Распечатать всё из таблицы, сортировка по model
SELECT model,
    aircraft_code,
    range
FROM aircrafts
ORDER BY model;
--
-- Распечатать всё из таблицы, условие - дальность полёта
SELECT model,
    aircraft_code,
    range
FROM aircrafts
WHERE range >= 4000
    and range <= 6000;
--
```

```sql
-- Удалить строку
DELETE FROM aircrafts WHERE aircraft_code = 'CN1';
--
-- Удалить строку с условние
DELETE FROM aircrafts
WHERE range > 10000
    OR range < 3000;
--
-- Удалить все строки из таблицы
DELETE FROM aircrafts;
--
```

```sql
-- Обновить дальность полёта SU9 на 500 км
UPDATE aircrafts
SET range = 3500
WHERE aircraft_code = 'SU9';
--
```


