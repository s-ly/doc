# Связывания статической библиотеки

### Пример создания и связывания статической библиотеки.

```makefile
GCC = gcc -Wall -Werror -Wextra -std=c11
AR = ar rcs

all: rebuild

rebuild: clean stat_lib obj_stat

obj_stat: app_stat.o
    $(GCC) -L. -lfunc_stat -o app_stat app_stat.o

clean:
    rm -rf app
    rm -rf *.o
    rm -rf *.a
    rm -rf *.gch
    rm -rf app_stat

stat: func.c
    $(GCC) -c func.c -o func.o

stat_lib: stat
    $(AR) libfunc_stat.a func.o
```

### Описание:

**stat_lib** -> вызывает **stat**.  

**stat** -> компилируем func.c в func.o (в объектный файл).  

**stat_lib** -> создаём статическую библиотеку libfunc_stat.a из func.o.  

**obj_stat** -> связывает app_stat.o с библиотекой libfunc_stat.a для создания исполняемого файла app_stat.

### Дополнительно:

Автоматическое правило: make имеет встроенные правила для некоторых типов файлов. Для файлов с расширением .o, make знает, что они должны быть скомпилированы из соответствующих файлов с расширением .c с помощью компилятора C. Таким образом, если app_stat.c существует и app_stat.o не найден или устарел, make автоматически выполнит команду для его создания.

Имя файла статической библиотеки C не обязательно должно начинаться с «lib». Однако принято добавлять к имени префикс «lib», особенно при использовании этой -l опции во время связывания. Это связано с тем, что компоновщик ожидает, что библиотека будет названа в форме lib{name}.a для статических библиотек и lib{name}.soдля общих библиотек. Когда вы используете эту -l опцию с компоновщиком, он автоматически добавляет lib префикс и соответствующий суффикс (.a для статических библиотек) при поиске библиотеки. Например, если вы напишете -lfoo, компоновщик будет искать файл с именем libfoo.a. Если вы хотите использовать библиотеку, которая не соответствует lib соглашению о префиксах, вам придется указать полный путь к файлу библиотеки при связывании, не используя -l опцию.

-L. флаг, указывающий компоновщику искать библиотеки в текущем каталоге. Флаг -lfunc_stat для связывания с libfunc_stat.a библиотекой.

### Файлы проекта:

##### app_stat.c:

```c
#include<stdio.h>
#include"func.h"

int main() {
    printf("Hello static\n");
    my_print();
    return 0;
}
```

##### func.c:

```c
#include"func.h"

void my_print(){
    printf("Hello my func\n");
}
```

##### func.h:

```c
#include<stdio.h>

void my_print();
```

# Связывание нескольких файлов

### Связывание нескольких файлов

```makefile
GCC = gcc -Wall -Werror -Wextra -std=c11

all: sum.o my_sum.o test_lib.o
    $(GCC) *.o -o app

sum.o:
    $(GCC) -c sum.c -o sum.o

my_sum.o:
    $(GCC) -c my_sum.c -o my_sum.o

test_lib.o:
    $(GCC) -c test_lib.c -o test_lib.o

clean:
    rm -rf *.o
```

### Файлы проекта:

##### sum.c

```c
#include"sum.h"

int sum(int x, int y){
    return x + y;
}
```

##### sum.h

```c
int sum(int x, int y);
```

##### my_sum.c

```c
#include"my_sum.h"

int my_sum(int x, int y){
    return x + y;
}
```

##### my_sum.h

```c
int my_sum(int x, int y);
```

##### test_lib.c

```c
#include<stdio.h>
#include"sum.h"
#include"my_sum.h"

int main(){
    int x = 10;
    int y = 20;
    int s1 = 0;
    int s2 = 0;

    s1 = sum(x, y);
    s2 = my_sum(x, y);

    printf("s1 = %d\n", s1);
    printf("s2 = %d\n", s2);

    return 0;
}
```

# WSL или Linux

### Определить WSL или обычный Linux

Например, что бы открыть браузер с результатами теста, команды будут разные. Для этого определяем какая это linux. Здесь **OPEN_CMD** определяется для разных версий linux.

```makefile
CC = gcc -Wall -Werror -Wextra -std=c11
OS = $(shell uname)

ifeq ($(OS),Linux)
    Flags = -lcheck  -lm -lrt -lpthread -lsubunit
else
    Flags = -lcheck -lm -lpthread
endif

ifeq ($(shell uname -r | grep -q microsoft && echo WSL2 || echo Linux), WSL2)
    OPEN_CMD = explorer.exe .\\report\\index.html
else
    OPEN_CMD = open report/index.html
endif

all: clean s21_string.a

s21_string: clean s21_string.a test

s21_string.a:
    $(CC) -c s21_*.c $(Flags)
    ar rcs s21_string.a *.o
    ranlib s21_string.a
    rm *.o

test:
    $(CC) --coverage *.c test_dir/*.c -o test $(Flags)
    ./test

gcov_report: test
    ./test
    lcov -t "result" -o res.info -c -d .
    genhtml -o report res.info
    $(OPEN_CMD)

clean:
    rm -rf *.a *.o test *.gcno *.gcda report *.info

cn: 
    clang-format -style=Google -n *.c *.h
```
