# Ссылки

[Материалы по Си](https://21-school-by-students.notion.site/C-d28998ee5d9a4477b9595bae536bad84)

[База знаний Новосибирского кампуса](https://held-stingray-d76.notion.site/Home-Page-v2-0-15c0679976e14236b6bb64617a4f7048)

[s21_matrix_03_10_23_part_1.mp4 - Google Диск](https://drive.google.com/file/d/1w4Fx6wSyGOk0YKUw55IhEWF4BDqluAY3/view?usp=sharing)

[s21_matrix_03_10_23_part_2.mp4 - Google Диск](https://drive.google.com/file/d/1D6U1dlX-_1LFq3nIAyukBupra8xudQay/view?usp=sharing)

http://mathprofi.ru/deistviya_s_matricami.html

![matrixx.png](../img/matrixx.png)

[GitLab matrix](https://repos.21-school.ru/students/C6_s21_matrix.ID_353530/gentrifr_student.21_school.ru/C6_s21_matrix-1/-/blob/master/README_RUS.md)

<img src="../img/2024-03-26%2003-52-03.png" title="" alt="2024-03-26 03-52-03.png" width="170">

[Калькулятор матриц](https://programforyou.ru/calculators/calculator-matric)

![math1.png](../img/math1.png)

https://www.math10.com/ru/vysshaya-matematika/matrix/umnozhenie-matric.html

[Обратная матрица: метод Жордана-Гаусса - YouTube](https://www.youtube.com/watch?v=dFVJ6TRweMw) 

# Описание задания

Создание матриц

```c
int s21_create_matrix(int rows, int columns, matrix_t *result);
```

Очистка матриц

`void s21_remove_matrix(matrix_t *A);`

Сравнение матриц

`int s21_eq_matrix(matrix_t *A, matrix_t *B);`

Сложение и вычитание матриц

`int s21_sum_matrix(matrix_t *A, matrix_t *B, matrix_t *result);
int s21_sub_matrix(matrix_t *A, matrix_t *B, matrix_t *result);`

Умножение матрицы на число. Умножение двух матриц

`int s21_mult_number(matrix_t *A, double number, matrix_t *result);
int s21_mult_matrix(matrix_t *A, matrix_t *B, matrix_t *result);`

Транспонирование матрицы

`int s21_transpose(matrix_t *A, matrix_t *result);`

### Минор матрицы и матрица алгебраических дополнений

```c
int s21_calc_complements(matrix_t *A, matrix_t *result);
```

### Определитель матрицы

```c
int s21_determinant(matrix_t *A, double *result);
```

Определитель матрицы — это специальное число, которое может быть вычислено для квадратной матрицы.

$$
\text{det}(A) = \sum_{j=1}^{n} a_{ij} M_{ij}
$$

где $(a_{ij})$ — элементы матрицы (A), а $(M_{ij})$ — минор элемента $(a_{ij})$, который вычисляется как определитель матрицы, полученной путем удаления строки (i) и столбца (j) из (A).

##### 

##### Пример для 2x2 матрицы

Для матрицы размером 2x2, определитель вычисляется как:

$$
[ \text{det}(A) = a_{11}a_{22} - a_{12}a_{21} ]
$$

где $(a_{11}, a_{12}, a_{21}, a_{22})$ — элементы матрицы (A).

##### 

##### Пример для 3x3 матрицы

Для матрицы размером 3x3, определитель вычисляется как:

$$
[ \text{det}(A) = a_{11}(a_{22}a_{33} - a_{23}a_{32}) - a_{12}(a_{21}a_{33} - a_{23}a_{31}) + a_{13}(a_{21}a_{32} - a_{22}a_{31}) ]
$$

##### 

##### Вычисления определителя матрицы 3x3 методом Гаусса-Жордана

Есть матрица:

$$
A = \begin{bmatrix} 1.00 & 2.00 & 3.00 \\ 4.00 & 5.00 & 6.00 \\ 7.00 & 8.00 & 10.00 \end{bmatrix}
$$

Шаг 1: Копирование матрицы в временную матрицу

Сначала мы копируем исходную матрицу в временную матрицу `matrix` типа `long double`. Это делается для того, чтобы не изменять исходную матрицу во время вычислений.

Шаг 2: Выбор опорного элемента

Для каждой строки матрицы ищем опорный элемент (максимальный по модулю элемент в строке). В нашем случае, для первой строки опорный элемент - это `1.00`.

Шаг 3: Приведение матрицы к ступенчатому виду

Мы приводим матрицу к ступенчатому виду, вычитая из каждой строки умноженную на множитель строку с опорным элементом. Это делается для каждой строки, начиная со второй.

После приведения матрицы к ступенчатому виду, матрица выглядит так:

$$
A' = \begin{bmatrix} 1.00 & 2.00 & 3.00 \\ 0.00 & -3.00 & -6.00 \\ 0.00 & 0.00 & 0.00 \end{bmatrix}
$$

### Обратная матрица

`int s21_inverse_matrix(matrix_t *A, matrix_t *result);`
