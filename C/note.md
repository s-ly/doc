# The #define Guard

Все файлы заголовков должны иметь защиту **#define** для предотвращения многократного включения. Формат имени символа должен быть `<PROJECT>_<PATH>_<FILE>_H_`.

Чтобы гарантировать уникальность, они должны основываться на полном пути в дереве исходного кода проекта. Например, файл **foo/src/bar/baz.h** в проекте **foo** должен иметь следующую защиту:

```c
#ifndef FOO_BAR_BAZ_H_
#define FOO_BAR_BAZ_H_
...
#endif  // FOO_BAR_BAZ_H_
```

[Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html#The__define_Guard)

### **Пример школьного проекта:**

**C5_S21_DECIMAL_3_** — имя проекта
**TESTS_** — подпапка tests в папке src
**S21_TEST_H_** — имя файла s21_test.h

```c
#define C5_S21_DECIMAL_3_S21_DECIMAL_H_
#define C5_S21_DECIMAL_3_TESTS_S21_TEST_H_
```


