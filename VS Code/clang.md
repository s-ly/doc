# Для форматирования нужен clang-format

Для начала его нужно установить в систему:

`sudo apt install clang-format`

Создать файл в директоии проекта:

`.clang-format`

Содержание:

```
---
BasedOnStyle: Google

```

Проверить:

`clang-format -n app.cpp`

Исправить:

`clang-format -i app.cpp`




