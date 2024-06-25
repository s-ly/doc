# Подсветка синтаксиса в терминале

`pygments` - утилита python, для подсветки синтаксиса в терминале.

## Установка

`sudo apt install python3-pygments`

## Использование

Подсветить в файле:

`cat example.py | pygmentize`

Подсветить в файле конкретный язык программирования:

`cat example.py | pygmentize -l python`

Так тоже сработает:

`pygmentize -l python example.py`

Генерирует html:

`cat example.py | pygmentize -f html`


