# Установить библиотеку math.h

Если компилятор не может найти math.h:

```bash
s21_matrix.h:4:10: fatal error: math.h: Нет такого файла или каталога
    4 | #include <math.h>
      |          ^~~~~~~~
compilation terminated.
```

Установка: 

```bash
sudo apt-get install build-essential
```


