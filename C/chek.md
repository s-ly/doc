# Модульные Unit тесты

## Главная программа тестирования test.c

В ней создаётся массив **Наборов (Suite)**. Потом циклом прогоняется этот массив наборов. И подсчитывается кол-во ошибок. Эта программа компилируется в исполняемый файл теста.

```c
#include "test.h"

int main(void){
    int fail = 0;
    Suite *sum_tests[] = {
        t_summ(), NULL
    };

    for (int i = 0; sum_tests[i] != NULL; i++){
        SRunner *sr = srunner_create(sum_tests[i]);
        srunner_set_fork_status(sr, CK_NOFORK);
        srunner_run_all(sr, CK_NORMAL);
        fail += srunner_ntests_failed(sr);
        srunner_free(sr);
    }
    printf("FAILED^ %d", fail);

    if (fail == 0) {
        return 0;
    } else {
        return 1;
    }

}
```

### test.h

Suite ***t_summ**(void); — сигнатура тестового набора.
**sum.h** — модуль с образцовой функцией **int sum(int x, int y);**
**my_sum.h** — содержит тестируемую функцию **int my_sum(int x, int y);**

```c
#ifndef SRC_TEST_H
#define SRC_TEST_H

#include <check.h>
#include <limits.h>
#include <stdio.h>
#include <string.h>

#include"../sum.h"
#include"../my_sum.h"

Suite *t_summ(void);

#endif
```

### Тестовые функции и набор, в который они войдут test_summa.c

**ck_assert_int_eq**(sum(x, y), my_sum(x, y)); сравнивает возвращаемые значения образцовой и тестируемой функций. Они должны быть равны.

```c
#include "test.h"

START_TEST(sum_1){
    int x = 2;
    int y = 3;
    ck_assert_int_eq(sum(x, y), my_sum(x, y));
}
END_TEST

START_TEST(sum_2){
    int x = 20;
    int y = 30;
    ck_assert_int_eq(sum(x, y), my_sum(x, y));
}
END_TEST

Suite *t_summ(void){
    Suite *s = suite_create("SUMM");
    TCase *tc = tcase_create("sum_tc");
    tcase_add_test(tc, sum_1);
    tcase_add_test(tc, sum_2);
    suite_add_tcase(s, tc);
    return s;
}
```

### Сборка Makefile

Цель **test** - компилирует тестирующую программу и запускает её.
Цель **gcov_report** - тоже сперва компелирует тестирующую программу и запускает её, потом создаёт html документ с результатом тестирования.

```makefile
GCC = gcc -Wall -Werror -Wextra -std=c11
OS = $(shell uname)
FILES = sum.c my_sum.c

ifeq ($(OS),Linux)
    Flags = -lcheck  -lm -lrt -lpthread -lsubunit
else
    Flags = -lcheck -lm -lpthread
endif

all: sum.o my_sum.o test_lib.o
    $(GCC) *.o -o app

sum.o:
    $(GCC) -c sum.c -o sum.o

my_sum.o:
    $(GCC) -c my_sum.c -o my_sum.o

test_lib.o:
    $(GCC) -c test_lib.c -o test_lib.o

test:
    $(GCC) --coverage $(FILES) unit_test/*.c -o test $(Flags)
    ./test

gcov_report: test
    ./test
    lcov -t "result" -o res.info -c -d .
    genhtml -o report res.info
    open report/index.html

clean:
    rm -rf *.o
    rm -rf *.gcno
    rm -rf *.gcda
    rm -rf app
    rm -rf test
    rm -rf report
    rm -rf *.info
```

# Ссылки

[Check (библиотека модульного тестирования) — Википедия](https://ru.wikipedia.org/wiki/Check_(%D0%B1%D0%B8%D0%B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA%D0%B0_%D0%BC%D0%BE%D0%B4%D1%83%D0%BB%D1%8C%D0%BD%D0%BE%D0%B3%D0%BE_%D1%82%D0%B5%D1%81%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F))

# Установка

```bash
sudo apt update
sudo apt install check
```

Проверка:

```bash
pkg-config --modversion check
```

Дополнительно:

```bash
sudo apt-get install lcov
```


