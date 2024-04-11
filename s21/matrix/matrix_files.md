# Файлы проекта

### s21_matrix.h

```c
#ifndef S21_MATRIX_H
#define S21_MATRIX_H
#include <math.h>
#include <stdio.h>
#include <stdlib.h>

typedef struct matrix_struct {
  double **matrix;
  int rows;
  int columns;
} matrix_t;

#define SUCCESS 1
#define FAILURE 0
#define OK 0
#define ERROR_MATRIX 1
#define ERROR_CALC 2

// enum ERROR_CODES { OK, ERROR_MATRIX, ERROR_CALC };

int s21_create_matrix(int rows, int columns, matrix_t *result);
void s21_remove_matrix(matrix_t *A);
int s21_eq_matrix(matrix_t *A, matrix_t *B);
int s21_sum_matrix(matrix_t *A, matrix_t *B, matrix_t *result);
int s21_sub_matrix(matrix_t *A, matrix_t *B, matrix_t *result);
int s21_mult_number(matrix_t *A, double number, matrix_t *result);
int s21_mult_matrix(matrix_t *A, matrix_t *B, matrix_t *result);
int s21_transpose(matrix_t *A, matrix_t *result);
int s21_calc_complements(matrix_t *A, matrix_t *result);
int s21_determinant(matrix_t *A, double *result);
int s21_inverse_matrix(matrix_t *A, matrix_t *result);
int wrong_matrix(matrix_t *A);
matrix_t create_minor(matrix_t *A, int a, int b);
void s21_initialize_matrix(matrix_t *A, double start_value,
                           double iteration_step);

/////////////////////
void s21_print_matrix(const matrix_t *matrix);
#endif
```

### s21_arithmetic.c

```c
#include "s21_matrix.h"

//умножения двух матриц
int s21_mult_matrix(matrix_t *A, matrix_t *B, matrix_t *result) {
  int ans = 0;
  //Проверка корректности матриц:
  if (wrong_matrix(A) || wrong_matrix(B)) {
    ans = 1;
    //Проверка размеров матриц:
  } else if (A->columns != B->rows) {
    ans = 2;
  } else {
    //Создание матрицы для результата
    if (s21_create_matrix(A->rows, B->columns, result))
      ans = 1;
    else {
      for (int i = 0; i < A->rows; ++i) {
        for (int j = 0; j < B->columns; ++j) {
          for (int k = 0; k < A->columns; ++k) {
            //Умножение матриц
            result->matrix[i][j] += A->matrix[i][k] * B->matrix[k][j];
            //Проверка на переполнение
            if (result->matrix[i][j] >= INFINITY ||
                result->matrix[i][j] <= -INFINITY) {
              ans = 2;
            }
          }
        }
      }
    }
  }
  return ans;
}

//умножения каждого элемента матрицы A на заданное число
int s21_mult_number(matrix_t *A, double number, matrix_t *result) {
  int ans = 0;
  //Проверка корректности матрицы и указателя на результат:
  if (wrong_matrix(A) || result == NULL) {
    ans = 1;
    //Проверка числа
  } else if (number >= INFINITY || number <= -INFINITY) {
    ans = 2;
  } else {
    //Создание матрицы для результата
    if (s21_create_matrix(A->rows, A->columns, result)) {
      ans = 1;
    } else {
      for (int i = 0; i < A->rows && !ans; ++i) {
        for (int j = 0; j < A->columns && !ans; ++j) {
          //Умножение матрицы на число
          result->matrix[i][j] = A->matrix[i][j] * number;
          //Проверка на переполнение
          if (result->matrix[i][j] == INFINITY ||
              result->matrix[i][j] == -INFINITY) {
            ans = 2;
          }
        }
      }
    }
  }
  return ans;
}

int s21_sub_matrix(matrix_t *A, matrix_t *B, matrix_t *result) {
  int ans = 0;
  if (A->columns != B->columns || A->rows != B->rows) {
    ans = 2;
  } else if (wrong_matrix(A) || wrong_matrix(B)) {
    ans = 1;
  } else {
    if (s21_create_matrix(A->rows, A->columns, result)) {
      ans = 1;
    } else {
      for (int i = 0; i < A->rows; ++i) {
        for (int j = 0; j < A->columns; ++j) {
          result->matrix[i][j] = A->matrix[i][j] - B->matrix[i][j];
          if (result->matrix[i][j] >= INFINITY ||
              result->matrix[i][j] <= -INFINITY) {
            ans = 2;
          }
        }
      }
    }
  }
  return ans;
}

//сложения двух матриц
int s21_sum_matrix(matrix_t *A, matrix_t *B, matrix_t *result) {
  int ans = 0;
  //Проверка размеров матриц: количество строк и столбцов матриц A и B
  //совпадают. Если размеры не совпадают, функция возвращает 2.
  if (A->columns != B->columns || A->rows != B->rows) {
    ans = 2;
  } else if (wrong_matrix(A) || wrong_matrix(B)) {
    ans = 1;
  } else {
    //Создание матрицы для результата:
    //Если создание матрицы не удаётся, функция возвращает 1.
    if (s21_create_matrix(A->rows, A->columns, result)) {
      ans = 1;
    } else {
      for (int i = 0; i < A->rows; ++i) {
        for (int j = 0; j < A->columns; ++j) {
          //Сложение матриц:
          result->matrix[i][j] = A->matrix[i][j] + B->matrix[i][j];
          //Проверка на переполнение:
          if (result->matrix[i][j] >= INFINITY ||
              result->matrix[i][j] <= -INFINITY) {
            ans = 2;
          }
        }
      }
    }
  }
  return ans;
}
```

### s21_another_func.c

```c
#include <stdio.h>

#include "s21_matrix.h"

// создания и инициализации матрицы с заданным количеством строк и столбцов.
int s21_create_matrix(int rows, int columns, matrix_t *result) {
  int ans = 0;
  //Проверка входных параметров: Функция сначала проверяет, что количество строк
  //и столбцов больше нуля. Если это не так, функция возвращает код ошибки 1 и
  //устанавливает result->matrix в NULL.
  if (rows < 1 || columns < 1) {
    ans = 1;
    result->matrix = NULL;
    //Проверка указателя на результат: Если указатель result равен NULL, функция
    //также возвращает код ошибки 1.
  } else if (result == NULL) {
    ans = 1;
  } else {
    //Инициализация структуры: Если входные параметры корректны,
    //функция инициализирует структуру result заданными значениями количества
    //строк и столбцов.
    result->rows = rows;
    result->columns = columns;
    //Выделение памяти для матрицы: Функция выделяет память под матрицу с
    //помощью функции calloc. calloc выделяет память и инициализирует её нулями.
    //Первый вызов calloc выделяет память под указатели на строки матрицы.
    result->matrix = (double **)calloc(rows, sizeof(double *));
    for (int i = 0; i < rows; i++) {
      //Второй вызов calloc в цикле выделяет память под каждую строку матрицы.
      result->matrix[i] = (double *)calloc(columns, sizeof(double));
    }
  }
  return ans;
}

//освобождения памяти, выделенной для двумерного массива (матрицы)
void s21_remove_matrix(matrix_t *A) {
  //Функция сначала проверяет, что указатель A не равен NULL
  //и что указатель на матрицу A->matrix также не равен NULL.
  //Это необходимо для предотвращения ошибок доступа к памяти.
  if (A != NULL && A->matrix != NULL) {
    //Освобождение памяти для каждой строки матрицы
    for (int i = 0; i < A->rows; i++) free(A->matrix[i]);
    //Освобождение памяти для массива указателей на строки
    free(A->matrix);
    //обнуляет количество строк и столбцов в структуре matrix_t и устанавливает
    //указатель на матрицу в NULL. Это делается для того, чтобы структура
    // matrix_t не содержала указателей на уже освобожденную память, что могло
    // бы привести к ошибкам при дальнейшем использовании.
    A->columns = 0;
    A->rows = 0;
    A->matrix = NULL;
  }
}

//сравнения двух матриц
int s21_eq_matrix(matrix_t *A, matrix_t *B) {
  int ans = SUCCESS;
  //проверяет, что количество строк и столбцов матриц A и B совпадает
  //Проверка корректности матриц: wrong_matrix(A)
  if (A->columns != B->columns || A->rows != B->rows || wrong_matrix(A) ||
      wrong_matrix(B)) {
    ans = FAILURE;
  } else {
    for (int i = 0; i < A->rows && ans; ++i) {
      for (int j = 0; j < A->columns && ans; ++j) {
        //Сравнение элементов с точностью: Для сравнения элементов матриц
        //используется функция fabs, которая возвращает абсолютное значение
        //разности двух чисел. Если разность между элементами матриц A и B в
        //позиции [i][j] больше или равна 1e-7 (что означает, что элементы не
        //равны с заданной точностью), функция устанавливает ans в FAILURE.
        if ((fabs(A->matrix[i][j] - B->matrix[i][j])) >= 1e-7) ans = FAILURE;
      }
    }
  }
  return ans;
}

//транспонирования матрицы
int s21_transpose(matrix_t *A, matrix_t *result) {
  int ans = 0;
  //Проверка корректности матрицы
  if (wrong_matrix(A)) {
    ans = 1;
  } else {
    //Создание матрицы для результата
    if (s21_create_matrix(A->columns, A->rows, result)) {
      ans = 1;
    } else {
      for (int i = 0; i < A->rows; ++i) {
        for (int j = 0; j < A->columns; ++j)
          //Транспонирование матрицы:
          result->matrix[j][i] = A->matrix[i][j];
      }
    }
  }
  return ans;
}

int s21_calc_complements(matrix_t *A, matrix_t *result) {
  int ans = 0;

  if (A == NULL || result == NULL || A->columns <= 0 || A->rows <= 0) {
    ans = 1;
  }
  if (A->rows != A->columns) {
    ans = 2;
  }
  if (!ans) {
    s21_create_matrix(A->rows, A->columns, result);
    for (int i = 0; i < A->rows && !ans; ++i) {
      for (int j = 0; j < A->columns && !ans; ++j) {
        matrix_t tmp;
        // вычисляю минор
        tmp = create_minor(A, i, j);
        double det = 0;
        if (!s21_determinant(&tmp, &det)) {
          // вычисление алгебр. дополнения из минора
          result->matrix[i][j] = pow(-1, (i + j + 2)) * det;
        } else {
          ans = 2;
        }
        s21_remove_matrix(&tmp);
      }
    }
  }
  return ans;
}

int s21_inverse_matrix(matrix_t *A, matrix_t *result) {
  int ans = 0;
  double det = 1;
  ans = s21_determinant(A, &det);
  if (wrong_matrix(A)) {
    ans = 1;
  }
  if ((A->columns != A->rows) || (det == 0)) {
    ans = 2;
  }
  if (!ans) {
    if (A->columns == 1) {
      ans = s21_create_matrix(A->rows, A->columns, result);
      result->matrix[0][0] = 1 / A->matrix[0][0];
    } else {
      matrix_t tmp;
      matrix_t tmp2;
      // матрица алгебр. длпполнений
      ans = s21_calc_complements(A, &tmp);
      // транспонируем
      ans = s21_transpose(&tmp, &tmp2);
      // обратная по формуле
      ans = s21_mult_number(&tmp2, 1.0 / det, result);
      s21_remove_matrix(&tmp);
      s21_remove_matrix(&tmp2);
    }
  }
  return ans;
}

```

### s21_determinant.c

```c
#include "s21_matrix.h"

// вычисляет определитель матрицы
int s21_determinant(matrix_t *A, double *result) {
  int ans = 0;
  // Проверка корректности матрицы
  if (wrong_matrix(A)) {
    ans = 1;
    // Проверка размеров матрицы
  } else if (A->columns != A->rows) {
    ans = 2;
  } else {
    // Если матрица A является матрицей 1x1, определитель равен единственному
    // элементу матрицы.
    if (A->columns == 1) *result = A->matrix[0][0];
    // Если матрица A является матрицей 2x2, определитель вычисляется как
    // произведение элементов главной диагонали минус произведение элементов
    // побочной диагонали.
    else if (A->columns == 2)
      *result =
          A->matrix[0][0] * A->matrix[1][1] - A->matrix[0][1] * A->matrix[1][0];
    else {
      // метод Гаусса для вычисления определителя
      // последовательное умножение строк матрицы на скаляры
      // и вычитание строк для приведения матрицы к треугольному виду,
      // после чего определитель вычисляется как произведение элементов главной
      // диагонали.
      matrix_t temp;
      s21_mult_number(A, 1, &temp);
      double d = 1;
      for (int row = 0; row < A->rows; ++row) {
        int max_row = row;
        for (int col = row + 1; col < A->columns; ++col) {
          if (fabs(A->matrix[max_row][col]) < fabs(A->matrix[row][col])) {
            max_row = col;
          }
          printf("MAX ROW = %d\n", max_row);
        }
        if (max_row != row) {
          for (int col = 0; col < A->rows; ++col) {
            double tmp = temp.matrix[row][col];
            temp.matrix[row][col] = temp.matrix[max_row][col];
            temp.matrix[max_row][col] = tmp;
            printf("TMP = %f\n", tmp);
          }
          d *= -1;
        }
        d *= temp.matrix[row][row];
        for (int i = row + 1; i < A->rows; ++i) {
          double multiplier = temp.matrix[i][row] / temp.matrix[row][row];
          printf("multiplier = %f\n", multiplier);
          for (int j = row; j < A->columns; ++j) {
            temp.matrix[i][j] -= multiplier * temp.matrix[row][j];
            ///
            s21_print_matrix(&temp);
          }
        }
      }
      *result = d;
      s21_remove_matrix(&temp);
    }
  }
  return ans;
}

```

### s21_service.c

```c
#include "s21_matrix.h"

//является ли структура matrix_t *A некорректной или неинициализированной
//Она возвращает 1 (или true), если матрица некорректна
int wrong_matrix(matrix_t *A) {
  return (A == NULL || A->columns < 1 || A->rows < 1 || A->matrix == NULL);
}

matrix_t create_minor(matrix_t *A, int a, int b) {
  matrix_t ans;
  // создание новой уменьшенной на 1 матрицы
  s21_create_matrix(A->rows - 1, A->columns - 1, &ans);
  // Проверка исходной матрицы
  if (wrong_matrix(A)) {
    ans.matrix = NULL;
    /*Заполнение новой матрицы: Если исходная матрица A корректна,
    функция начинает заполнять новую матрицу ans. Она проходит по каждой
    строке и столбцу исходной матрицы A, пропуская строку и столбец,
    соответствующие индексам a и b. Элементы, не находящиеся в этих строке
    и столбце, копируются в новую матрицу ans.*/
  } else {
    int r = 0;
    for (int i = 0; i < A->rows; ++i) {
      if (i != a) {
        int c = 0;
        for (int j = 0; j < A->columns; ++j) {
          if (j != b) {
            ans.matrix[r][c] = A->matrix[i][j];
            c++;
          }
        }
        r++;
      }
    }
  }
  return ans;
}

void s21_initialize_matrix(matrix_t *A, double start_value,
                           double iteration_step) {
  if (A != NULL && A->matrix != NULL) {
    double value = start_value;
    for (int i = 0; i < A->rows; i++) {
      for (int j = 0; j < A->columns; j++) {
        A->matrix[i][j] = value;
        value += iteration_step;
      }
    }
  }
}

//////////////////
void s21_print_matrix(const matrix_t *matrix) {
  if (matrix == NULL || matrix->matrix == NULL) {
    printf("Matrix is NULL or not initialized.\n");
    return;
  }

  printf("-----------------\n");
  for (int i = 0; i < matrix->rows; i++) {
    for (int j = 0; j < matrix->columns; j++) {
      // Вывод каждого элемента с двумя знаками после запятой
      printf("%.2f ", matrix->matrix[i][j]);
    }
    printf("\n");  // Переход на новую строку после каждой строки матрицы
  }
}
```

### test.c

```c
#include <check.h>

#include "../s21_matrix.h"

/*  TEST OPTIONS */

START_TEST(s21_create_matrix_1) {
  // success creation
  matrix_t A = {};
  ck_assert_int_eq(s21_create_matrix(5, 5, &A), OK);
  s21_remove_matrix(&A);
}
END_TEST

START_TEST(s21_create_matrix_2) {
  // failure null pointer
  ck_assert_int_eq(s21_create_matrix(5, 5, NULL), ERROR_MATRIX);
}
END_TEST

START_TEST(s21_create_matrix_3) {
  // failure incorrect rows/columns
  matrix_t A = {};
  ck_assert_int_eq(s21_create_matrix(5, 0, &A), ERROR_MATRIX);
}
END_TEST

START_TEST(s21_remove_matrix_1) {
  // success remove
  matrix_t A = {};
  s21_create_matrix(5, 5, &A);
  s21_remove_matrix(&A);
  ck_assert_ptr_null(A.matrix);
}
END_TEST

START_TEST(s21_remove_matrix_2) {
  // failure handle null pointer
  s21_remove_matrix(NULL);
  ck_assert_int_eq(1, 1);
}
END_TEST

START_TEST(s21_remove_matrix_3) {
  // failure handle ERROR_MATRIX structure
  matrix_t A = {};
  s21_remove_matrix(&A);
  ck_assert_int_eq(1, 1);
}
END_TEST

START_TEST(s21_eq_matrix_1) {
  // success with initialized values
  matrix_t A = {};
  matrix_t B = {};
  s21_create_matrix(5, 5, &A);
  s21_create_matrix(5, 5, &B);
  s21_initialize_matrix(&A, 21, 21);
  s21_initialize_matrix(&B, 21, 21);
  ck_assert_int_eq(s21_eq_matrix(&A, &B), SUCCESS);
  s21_remove_matrix(&A);
  s21_remove_matrix(&B);
}
END_TEST

START_TEST(s21_eq_matrix_2) {
  // success with uninitialized values
  matrix_t A = {};
  matrix_t B = {};
  s21_create_matrix(5, 5, &A);
  s21_create_matrix(5, 5, &B);
  ck_assert_int_eq(s21_eq_matrix(&A, &B), SUCCESS);
  s21_remove_matrix(&A);
  s21_remove_matrix(&B);
}
END_TEST

START_TEST(s21_eq_matrix_3) {
  // success with a difference of less than 1e-7
  matrix_t A = {};
  matrix_t B = {};
  s21_create_matrix(5, 5, &A);
  s21_create_matrix(5, 5, &B);
  s21_initialize_matrix(&A, 1, 0.000000001);
  s21_initialize_matrix(&B, 1, 0.000000002);
  ck_assert_int_eq(s21_eq_matrix(&A, &B), SUCCESS);
  s21_remove_matrix(&A);
  s21_remove_matrix(&B);
}
END_TEST

START_TEST(s21_eq_matrix_4) {
  // failure with different dimensions of matrices
  matrix_t A = {};
  matrix_t B = {};
  s21_create_matrix(5, 5, &A);
  s21_create_matrix(3, 4, &B);
  ck_assert_int_eq(s21_eq_matrix(&A, &B), FAILURE);
  s21_remove_matrix(&A);
  s21_remove_matrix(&B);
}
END_TEST

START_TEST(s21_eq_matrix_5) {
  // failure with ERROR_MATRIX
  matrix_t A = {};
  matrix_t B = {};
  s21_create_matrix(5, 5, &A);
  ck_assert_int_eq(s21_eq_matrix(&A, &B), FAILURE);
  s21_remove_matrix(&A);
}
END_TEST

START_TEST(s21_eq_matrix_6) {
  // failure with different values
  matrix_t A = {};
  matrix_t B = {};
  s21_create_matrix(5, 5, &A);
  s21_create_matrix(5, 5, &B);
  s21_initialize_matrix(&A, 21, 1);
  s21_initialize_matrix(&B, 42, 1);
  ck_assert_int_eq(s21_eq_matrix(&A, &B), FAILURE);
  s21_remove_matrix(&A);
  s21_remove_matrix(&B);
}
END_TEST

START_TEST(s21_sum_matrix_1) {
  // failure error with wrong matrices
  matrix_t A = {};
  matrix_t B = {};
  ck_assert_int_eq(s21_sum_matrix(&A, &B, NULL), ERROR_MATRIX);
}
END_TEST

START_TEST(s21_sum_matrix_2) {
  // failure with different dimensions of matrices
  matrix_t A = {};
  matrix_t B = {};
  matrix_t result = {};
  s21_create_matrix(4, 4, &A);
  s21_create_matrix(5, 5, &B);
  ck_assert_int_eq(s21_sum_matrix(&A, &B, &result), ERROR_CALC);
  s21_remove_matrix(&A);
  s21_remove_matrix(&B);
}
END_TEST

START_TEST(s21_sum_matrix_3) {
  // failure with non-finite value
  matrix_t A = {};
  matrix_t B = {};
  matrix_t result = {};
  s21_create_matrix(5, 5, &A);
  s21_create_matrix(5, 5, &B);
  s21_initialize_matrix(&A, 1, 1);
  s21_initialize_matrix(&B, 1, 1);
  B.matrix[3][3] = INFINITY;
  ck_assert_int_eq(s21_sum_matrix(&A, &B, &result), ERROR_CALC);
  s21_remove_matrix(&A);
  s21_remove_matrix(&B);
  s21_remove_matrix(&result);
}
END_TEST

START_TEST(s21_sum_matrix_4) {
  // sucess with initialized values
  matrix_t A = {};
  matrix_t B = {};
  matrix_t result = {};
  matrix_t eq_matrix = {};
  s21_create_matrix(5, 5, &A);
  s21_create_matrix(5, 5, &B);
  s21_create_matrix(5, 5, &eq_matrix);
  s21_initialize_matrix(&A, 1, 1);
  s21_initialize_matrix(&B, 1, 1);
  s21_initialize_matrix(&eq_matrix, 2, 2);
  ck_assert_int_eq(s21_sum_matrix(&A, &B, &result), OK);
  ck_assert_int_eq(s21_eq_matrix(&result, &eq_matrix), SUCCESS);
  s21_remove_matrix(&A);
  s21_remove_matrix(&B);
  s21_remove_matrix(&result);
  s21_remove_matrix(&eq_matrix);
}
END_TEST

START_TEST(s21_sum_matrix_5) {
  // sucess with uninitialized values
  matrix_t A = {};
  matrix_t B = {};
  matrix_t result = {};
  matrix_t eq_matrix = {};
  s21_create_matrix(5, 5, &A);
  s21_create_matrix(5, 5, &B);
  s21_create_matrix(5, 5, &eq_matrix);
  ck_assert_int_eq(s21_sum_matrix(&A, &B, &result), OK);
  ck_assert_int_eq(s21_eq_matrix(&result, &eq_matrix), SUCCESS);
  s21_remove_matrix(&A);
  s21_remove_matrix(&B);
  s21_remove_matrix(&result);
  s21_remove_matrix(&eq_matrix);
}
END_TEST

START_TEST(s21_sum_matrix_6) {
  // sucess with task refence
  matrix_t A = {};
  matrix_t B = {};
  matrix_t result = {};
  matrix_t eq_matrix = {};
  s21_create_matrix(3, 3, &A);
  s21_create_matrix(3, 3, &B);
  s21_create_matrix(3, 3, &eq_matrix);
  A.matrix[0][0] = 1, A.matrix[0][1] = 2, A.matrix[0][2] = 3;
  A.matrix[1][0] = 0, A.matrix[1][1] = 4, A.matrix[1][2] = 5;
  A.matrix[2][0] = 0, A.matrix[2][1] = 0, A.matrix[2][2] = 6;
  B.matrix[0][0] = 1, B.matrix[0][1] = 0, B.matrix[0][2] = 0;
  B.matrix[1][0] = 2, B.matrix[1][1] = 0, B.matrix[1][2] = 0;
  B.matrix[2][0] = 3, B.matrix[2][1] = 4, B.matrix[2][2] = 1;
  ck_assert_int_eq(s21_sum_matrix(&A, &B, &result), OK);
  eq_matrix.matrix[0][0] = 2, eq_matrix.matrix[0][1] = 2,
  eq_matrix.matrix[0][2] = 3;
  eq_matrix.matrix[1][0] = 2, eq_matrix.matrix[1][1] = 4,
  eq_matrix.matrix[1][2] = 5;
  eq_matrix.matrix[2][0] = 3, eq_matrix.matrix[2][1] = 4,
  eq_matrix.matrix[2][2] = 7;
  ck_assert_int_eq(s21_eq_matrix(&result, &eq_matrix), SUCCESS);
  s21_remove_matrix(&A);
  s21_remove_matrix(&B);
  s21_remove_matrix(&result);
  s21_remove_matrix(&eq_matrix);
}
END_TEST

START_TEST(s21_sub_matrix_1) {
  // failure error with wrong matrices
  matrix_t A = {};
  matrix_t B = {};
  ck_assert_int_eq(s21_sub_matrix(&A, &B, NULL), ERROR_MATRIX);
}
END_TEST

START_TEST(s21_sub_matrix_2) {
  // failure with different dimensions of matrices
  matrix_t A = {};
  matrix_t B = {};
  matrix_t result = {};
  s21_create_matrix(4, 4, &A);
  s21_create_matrix(5, 5, &B);
  ck_assert_int_eq(s21_sub_matrix(&A, &B, &result), ERROR_CALC);
  s21_remove_matrix(&A);
  s21_remove_matrix(&B);
}
END_TEST

START_TEST(s21_sub_matrix_3) {
  // failure with non-finite value
  matrix_t A = {};
  matrix_t B = {};
  matrix_t result = {};
  s21_create_matrix(5, 5, &A);
  s21_create_matrix(5, 5, &B);
  s21_initialize_matrix(&A, 1, 1);
  s21_initialize_matrix(&B, 1, 1);
  B.matrix[3][3] = INFINITY;
  ck_assert_int_eq(s21_sub_matrix(&A, &B, &result), ERROR_CALC);
  s21_remove_matrix(&A);
  s21_remove_matrix(&B);
  s21_remove_matrix(&result);
}
END_TEST

START_TEST(s21_sub_matrix_4) {
  // sucess with initialized values
  matrix_t A = {};
  matrix_t B = {};
  matrix_t result = {};
  matrix_t eq_matrix = {};
  s21_create_matrix(5, 5, &A);
  s21_create_matrix(5, 5, &B);
  s21_create_matrix(5, 5, &eq_matrix);
  s21_initialize_matrix(&A, 1, 1);
  s21_initialize_matrix(&B, 1, 1);
  ck_assert_int_eq(s21_sub_matrix(&A, &B, &result), OK);
  ck_assert_int_eq(s21_eq_matrix(&result, &eq_matrix), SUCCESS);
  s21_remove_matrix(&A);
  s21_remove_matrix(&B);
  s21_remove_matrix(&result);
  s21_remove_matrix(&eq_matrix);
}
END_TEST

START_TEST(s21_sub_matrix_5) {
  // sucess with uninitialized values
  matrix_t A = {};
  matrix_t B = {};
  matrix_t result = {};
  matrix_t eq_matrix = {};
  s21_create_matrix(5, 5, &A);
  s21_create_matrix(5, 5, &B);
  s21_create_matrix(5, 5, &eq_matrix);
  ck_assert_int_eq(s21_sub_matrix(&A, &B, &result), OK);
  ck_assert_int_eq(s21_eq_matrix(&result, &eq_matrix), SUCCESS);
  s21_remove_matrix(&A);
  s21_remove_matrix(&B);
  s21_remove_matrix(&result);
  s21_remove_matrix(&eq_matrix);
}
END_TEST

START_TEST(s21_sub_matrix_6) {
  // sucess with task refence
  matrix_t A = {};
  matrix_t B = {};
  matrix_t result = {};
  matrix_t eq_matrix = {};
  s21_create_matrix(3, 3, &A);
  s21_create_matrix(3, 3, &B);
  s21_create_matrix(3, 3, &eq_matrix);
  A.matrix[0][0] = 1, A.matrix[0][1] = 2, A.matrix[0][2] = 3;
  A.matrix[1][0] = 0, A.matrix[1][1] = 4, A.matrix[1][2] = 5;
  A.matrix[2][0] = 0, A.matrix[2][1] = 0, A.matrix[2][2] = 6;
  B.matrix[0][0] = 1, B.matrix[0][1] = 0, B.matrix[0][2] = 0;
  B.matrix[1][0] = 2, B.matrix[1][1] = 0, B.matrix[1][2] = 0;
  B.matrix[2][0] = 3, B.matrix[2][1] = 4, B.matrix[2][2] = 1;
  ck_assert_int_eq(s21_sub_matrix(&A, &B, &result), OK);
  eq_matrix.matrix[0][0] = 0, eq_matrix.matrix[0][1] = 2,
  eq_matrix.matrix[0][2] = 3;
  eq_matrix.matrix[1][0] = -2, eq_matrix.matrix[1][1] = 4,
  eq_matrix.matrix[1][2] = 5;
  eq_matrix.matrix[2][0] = -3, eq_matrix.matrix[2][1] = -4,
  eq_matrix.matrix[2][2] = 5;
  ck_assert_int_eq(s21_eq_matrix(&result, &eq_matrix), SUCCESS);
  s21_remove_matrix(&A);
  s21_remove_matrix(&B);
  s21_remove_matrix(&result);
  s21_remove_matrix(&eq_matrix);
}
END_TEST

START_TEST(s21_mult_number_1) {
  // failure with ERROR_MATRIX
  matrix_t A = {};
  double number = 3.14;
  s21_create_matrix(3, 3, &A);
  s21_initialize_matrix(&A, 1, 1);
  ck_assert_int_eq(s21_mult_number(&A, number, NULL), ERROR_MATRIX);
  s21_remove_matrix(&A);
}
END_TEST

START_TEST(s21_mult_number_2) {
  // failure with non-finite input double
  matrix_t A = {};
  matrix_t result = {};
  double number = INFINITY;
  s21_create_matrix(3, 3, &A);
  s21_initialize_matrix(&A, 1, 1);
  ck_assert_int_eq(s21_mult_number(&A, number, &result), ERROR_CALC);
  s21_remove_matrix(&A);
}
END_TEST

START_TEST(s21_mult_number_3) {
  // failure with with the resulting non-finite
  matrix_t A = {};
  matrix_t result = {};
  double number = 3;
  s21_create_matrix(3, 3, &A);
  s21_initialize_matrix(&A, 1, 1);
  A.matrix[2][2] = INFINITY;
  ck_assert_int_eq(s21_mult_number(&A, number, &result), ERROR_CALC);
  s21_remove_matrix(&A);
  s21_remove_matrix(&result);
}
END_TEST

START_TEST(s21_mult_number_4) {
  // success with uninitialized values
  matrix_t A = {};
  matrix_t result = {};
  matrix_t eq_matrix = {};
  double number = 3;
  s21_create_matrix(3, 3, &A);
  s21_create_matrix(3, 3, &eq_matrix);
  ck_assert_int_eq(s21_mult_number(&A, number, &result), OK);
  ck_assert_int_eq(s21_eq_matrix(&result, &eq_matrix), SUCCESS);
  s21_remove_matrix(&A);
  s21_remove_matrix(&result);
  s21_remove_matrix(&eq_matrix);
}
END_TEST

START_TEST(s21_mult_number_5) {
  // success with initialized values
  matrix_t A = {};
  matrix_t result = {};
  matrix_t eq_matrix = {};
  double number = 3;
  s21_create_matrix(3, 3, &A);
  s21_initialize_matrix(&A, 1, 1);
  s21_create_matrix(3, 3, &eq_matrix);
  s21_initialize_matrix(&eq_matrix, 3, 3);
  ck_assert_int_eq(s21_mult_number(&A, number, &result), OK);
  ck_assert_int_eq(s21_eq_matrix(&result, &eq_matrix), SUCCESS);
  s21_remove_matrix(&A);
  s21_remove_matrix(&result);
  s21_remove_matrix(&eq_matrix);
}
END_TEST

START_TEST(s21_mult_number_6) {
  // success with task reference values
  matrix_t A = {};
  matrix_t result = {};
  matrix_t eq_matrix = {};
  double number = 2;
  s21_create_matrix(3, 3, &A);
  A.matrix[0][0] = 1, A.matrix[0][1] = 2, A.matrix[0][2] = 3;
  A.matrix[1][0] = 0, A.matrix[1][1] = 4, A.matrix[1][2] = 2;
  A.matrix[2][0] = 2, A.matrix[2][1] = 3, A.matrix[2][2] = 4;
  s21_create_matrix(3, 3, &eq_matrix);
  eq_matrix.matrix[0][0] = 2, eq_matrix.matrix[0][1] = 4,
  eq_matrix.matrix[0][2] = 6;
  eq_matrix.matrix[1][0] = 0, eq_matrix.matrix[1][1] = 8,
  eq_matrix.matrix[1][2] = 4;
  eq_matrix.matrix[2][0] = 4, eq_matrix.matrix[2][1] = 6,
  eq_matrix.matrix[2][2] = 8;
  ck_assert_int_eq(s21_mult_number(&A, number, &result), OK);
  ck_assert_int_eq(s21_eq_matrix(&result, &eq_matrix), SUCCESS);
  s21_remove_matrix(&A);
  s21_remove_matrix(&result);
  s21_remove_matrix(&eq_matrix);
}
END_TEST

START_TEST(s21_mult_matrix_1) {
  // failure with ERROR_MATRIX
  matrix_t A = {};
  s21_create_matrix(3, 3, &A);
  s21_initialize_matrix(&A, 1, 1);
  ck_assert_int_eq(s21_mult_matrix(&A, NULL, NULL), ERROR_MATRIX);
  s21_remove_matrix(&A);
}
END_TEST

START_TEST(s21_mult_matrix_2) {
  // failure with with the resulting non-finite
  matrix_t A = {};
  matrix_t B = {};
  matrix_t result = {};
  s21_create_matrix(3, 3, &A);
  s21_create_matrix(3, 3, &B);
  s21_initialize_matrix(&A, 1, 1);
  B.matrix[2][2] = INFINITY;
  ck_assert_int_eq(s21_mult_matrix(&A, &B, &result), ERROR_CALC);
  s21_remove_matrix(&A);
  s21_remove_matrix(&B);
  s21_remove_matrix(&result);
}
END_TEST

START_TEST(s21_mult_matrix_3) {
  // failure with different dimensions of matrices
  matrix_t A = {};
  matrix_t B = {};
  matrix_t result = {};
  s21_create_matrix(3, 2, &A);
  s21_create_matrix(3, 2, &B);
  ck_assert_int_eq(s21_mult_matrix(&A, &B, &result), ERROR_CALC);
  s21_remove_matrix(&A);
  s21_remove_matrix(&B);
}
END_TEST

START_TEST(s21_mult_matrix_4) {
  // success with uninitialized values
  matrix_t A = {};
  matrix_t B = {};
  matrix_t result = {};
  matrix_t eq_matrix = {};
  s21_create_matrix(3, 3, &A);
  s21_create_matrix(3, 3, &B);
  s21_create_matrix(3, 3, &eq_matrix);
  ck_assert_int_eq(s21_mult_matrix(&A, &B, &result), OK);
  ck_assert_int_eq(s21_eq_matrix(&result, &eq_matrix), SUCCESS);
  s21_remove_matrix(&A);
  s21_remove_matrix(&B);
  s21_remove_matrix(&result);
  s21_remove_matrix(&eq_matrix);
}
END_TEST

START_TEST(s21_mult_matrix_5) {
  // success with initialized values
  matrix_t A = {};
  matrix_t B = {};
  matrix_t result = {};
  matrix_t eq_matrix = {};
  s21_create_matrix(3, 3, &A);
  s21_initialize_matrix(&A, 1, 1);
  s21_create_matrix(3, 3, &B);
  s21_initialize_matrix(&B, 1, 1);
  s21_create_matrix(3, 3, &eq_matrix);
  eq_matrix.matrix[0][0] = 30, eq_matrix.matrix[0][1] = 36,
  eq_matrix.matrix[0][2] = 42;
  eq_matrix.matrix[1][0] = 66, eq_matrix.matrix[1][1] = 81,
  eq_matrix.matrix[1][2] = 96;
  eq_matrix.matrix[2][0] = 102, eq_matrix.matrix[2][1] = 126,
  eq_matrix.matrix[2][2] = 150;
  ck_assert_int_eq(s21_mult_matrix(&A, &B, &result), OK);
  ck_assert_int_eq(s21_eq_matrix(&result, &eq_matrix), SUCCESS);
  s21_remove_matrix(&A);
  s21_remove_matrix(&B);
  s21_remove_matrix(&result);
  s21_remove_matrix(&eq_matrix);
}
END_TEST

START_TEST(s21_mult_matrix_6) {
  // success with task reference values
  matrix_t A = {};
  matrix_t B = {};
  matrix_t result = {};
  matrix_t eq_matrix = {};
  s21_create_matrix(3, 2, &A);
  A.matrix[0][0] = 1, A.matrix[0][1] = 4;
  A.matrix[1][0] = 2, A.matrix[1][1] = 5;
  A.matrix[2][0] = 3, A.matrix[2][1] = 6;
  s21_create_matrix(2, 3, &B);
  B.matrix[0][0] = 1, B.matrix[0][1] = -1, B.matrix[0][2] = 1;
  B.matrix[1][0] = 2, B.matrix[1][1] = 3, B.matrix[1][2] = 4;
  s21_create_matrix(3, 3, &eq_matrix);
  eq_matrix.matrix[0][0] = 9, eq_matrix.matrix[0][1] = 11,
  eq_matrix.matrix[0][2] = 17;
  eq_matrix.matrix[1][0] = 12, eq_matrix.matrix[1][1] = 13,
  eq_matrix.matrix[1][2] = 22;
  eq_matrix.matrix[2][0] = 15, eq_matrix.matrix[2][1] = 15,
  eq_matrix.matrix[2][2] = 27;
  ck_assert_int_eq(s21_mult_matrix(&A, &B, &result), OK);
  ck_assert_int_eq(s21_eq_matrix(&result, &eq_matrix), SUCCESS);
  s21_remove_matrix(&A);
  s21_remove_matrix(&B);
  s21_remove_matrix(&result);
  s21_remove_matrix(&eq_matrix);
}
END_TEST

START_TEST(s21_transpose_1) {
  // failure with ERROR_MATRIX
  matrix_t A = {};
  matrix_t result = {};
  ck_assert_int_eq(s21_transpose(&A, &result), ERROR_MATRIX);
}
END_TEST

START_TEST(s21_transpose_2) {
  // success with unininitialized matrix 3x2
  matrix_t A = {};
  matrix_t result = {};
  s21_create_matrix(3, 2, &A);
  ck_assert_int_eq(s21_transpose(&A, &result), OK);
  ck_assert_int_eq(result.rows, 2);
  ck_assert_int_eq(result.columns, 3);
  s21_remove_matrix(&A);
  s21_remove_matrix(&result);
}
END_TEST

START_TEST(s21_transpose_3) {
  // success with unininitialized matrix 2x3
  matrix_t A = {};
  matrix_t result = {};
  s21_create_matrix(2, 3, &A);
  ck_assert_int_eq(s21_transpose(&A, &result), OK);
  ck_assert_int_eq(result.rows, 3);
  ck_assert_int_eq(result.columns, 2);
  s21_remove_matrix(&A);
  s21_remove_matrix(&result);
}
END_TEST

START_TEST(s21_transpose_4) {
  // success with initialized matrix 2x3
  matrix_t A = {};
  matrix_t result = {};
  matrix_t eq_matrix = {};
  s21_create_matrix(2, 3, &A);
  s21_create_matrix(3, 2, &eq_matrix);
  s21_initialize_matrix(&A, 1, 1);
  ck_assert_int_eq(s21_transpose(&A, &result), OK);
  eq_matrix.matrix[0][0] = 1, eq_matrix.matrix[0][1] = 4;
  eq_matrix.matrix[1][0] = 2, eq_matrix.matrix[1][1] = 5;
  eq_matrix.matrix[2][0] = 3, eq_matrix.matrix[2][1] = 6;
  ck_assert_int_eq(s21_eq_matrix(&result, &eq_matrix), SUCCESS);
  ck_assert_int_eq(result.rows, 3);
  ck_assert_int_eq(result.columns, 2);
  s21_remove_matrix(&A);
  s21_remove_matrix(&eq_matrix);
  s21_remove_matrix(&result);
}
END_TEST

START_TEST(s21_transpose_5) {
  // success with task reference values and 3x2 matrix
  matrix_t A = {};
  matrix_t result = {};
  matrix_t eq_matrix = {};
  s21_create_matrix(3, 2, &A);
  s21_create_matrix(2, 3, &eq_matrix);
  A.matrix[0][0] = 1, A.matrix[0][1] = 4;
  A.matrix[1][0] = 2, A.matrix[1][1] = 5;
  A.matrix[2][0] = 3, A.matrix[2][1] = 6;
  ck_assert_int_eq(s21_transpose(&A, &result), OK);
  eq_matrix.matrix[0][0] = 1, eq_matrix.matrix[0][1] = 2,
  eq_matrix.matrix[0][2] = 3;
  eq_matrix.matrix[1][0] = 4, eq_matrix.matrix[1][1] = 5,
  eq_matrix.matrix[1][2] = 6;
  ck_assert_int_eq(s21_eq_matrix(&result, &eq_matrix), SUCCESS);
  ck_assert_int_eq(result.rows, 2);
  ck_assert_int_eq(result.columns, 3);
  s21_remove_matrix(&A);
  s21_remove_matrix(&eq_matrix);
  s21_remove_matrix(&result);
}
END_TEST

START_TEST(s21_calc_complements_1) {
  // failure with ERROR_MATRIX
  matrix_t A = {};
  s21_create_matrix(3, 3, &A);
  ck_assert_int_eq(s21_calc_complements(&A, NULL), ERROR_MATRIX);
  s21_remove_matrix(&A);
}
END_TEST

START_TEST(s21_calc_complements_2) {
  // failure with vector matrix (rows or cols == 1)
  matrix_t A = {};
  matrix_t result = {};
  s21_create_matrix(1, 3, &A);
  s21_initialize_matrix(&A, 1, 3);
  ck_assert_int_eq(s21_calc_complements(&A, &result), ERROR_CALC);
  s21_remove_matrix(&A);
}
END_TEST

START_TEST(s21_calc_complements_3) {
  // success with task reference values
  matrix_t A = {};
  matrix_t result = {};
  matrix_t eq_matrix = {};
  s21_create_matrix(3, 3, &A);
  s21_create_matrix(3, 3, &eq_matrix);
  A.matrix[0][0] = 1, A.matrix[0][1] = 2, A.matrix[0][2] = 3;
  A.matrix[1][0] = 0, A.matrix[1][1] = 4, A.matrix[1][2] = 2;
  A.matrix[2][0] = 5, A.matrix[2][1] = 2, A.matrix[2][2] = 1;
  ck_assert_int_eq(s21_calc_complements(&A, &result), OK);
  eq_matrix.matrix[0][0] = 0, eq_matrix.matrix[0][1] = 10,
  eq_matrix.matrix[0][2] = -20;
  eq_matrix.matrix[1][0] = 4, eq_matrix.matrix[1][1] = -14,
  eq_matrix.matrix[1][2] = 8;
  eq_matrix.matrix[2][0] = -8, eq_matrix.matrix[2][1] = -2,
  eq_matrix.matrix[2][2] = 4;
  ck_assert_int_eq(s21_eq_matrix(&result, &eq_matrix), SUCCESS);
  s21_remove_matrix(&A);
  s21_remove_matrix(&result);
  s21_remove_matrix(&eq_matrix);
}
END_TEST

START_TEST(s21_determinant_1) {
  // failure with ERROR_MATRIX
  matrix_t A = {};
  double det = 0;
  ck_assert_int_eq(s21_determinant(&A, &det), ERROR_MATRIX);
}
END_TEST

START_TEST(s21_determinant_2) {
  // failure with non-square matrix
  matrix_t A = {};
  double det = 0;
  s21_create_matrix(3, 2, &A);
  s21_initialize_matrix(&A, 1, 1);
  ck_assert_int_eq(s21_determinant(&A, &det), ERROR_CALC);
  s21_remove_matrix(&A);
}
END_TEST

START_TEST(s21_determinant_3) {
  // success with task reference values
  matrix_t A = {};
  double det = 0;
  s21_create_matrix(3, 3, &A);
  s21_initialize_matrix(&A, 1, 1);
  ck_assert_int_eq(s21_determinant(&A, &det), OK);
  ck_assert_double_eq(det, 0);
  s21_remove_matrix(&A);
}
END_TEST

START_TEST(s21_determinant_4) {
  // success with 2x2 matrix
  matrix_t A = {};
  double det = 0;
  s21_create_matrix(2, 2, &A);
  s21_initialize_matrix(&A, 3, 3);
  ck_assert_int_eq(s21_determinant(&A, &det), OK);
  ck_assert_double_eq(det, -18);
  s21_remove_matrix(&A);
}
END_TEST

START_TEST(s21_determinant_5) {
  // success with 1x1 matrix
  matrix_t A = {};
  double det = 0;
  s21_create_matrix(1, 1, &A);
  A.matrix[0][0] = 21;
  ck_assert_int_eq(s21_determinant(&A, &det), OK);
  ck_assert_double_eq(det, 21);
  s21_remove_matrix(&A);
}
END_TEST

START_TEST(s21_determinant_6) {
  // success with 4x4 matrix
  matrix_t A = {};
  double det = 0;
  s21_create_matrix(4, 4, &A);
  A.matrix[0][0] = 4, A.matrix[0][1] = 6, A.matrix[0][2] = -2,
  A.matrix[0][3] = 4;
  A.matrix[1][0] = 1, A.matrix[1][1] = 2, A.matrix[1][2] = -3,
  A.matrix[1][3] = 1;
  A.matrix[2][0] = 4, A.matrix[2][1] = -2, A.matrix[2][2] = 1,
  A.matrix[2][3] = 0;
  A.matrix[3][0] = 6, A.matrix[3][1] = 4, A.matrix[3][2] = 4,
  A.matrix[3][3] = 6;
  ck_assert_int_eq(s21_determinant(&A, &det), OK);
  ck_assert_double_eq(det, -144);
  s21_remove_matrix(&A);
}
END_TEST

START_TEST(s21_inverse_matrix_1) {
  // failure with ERROR_MATRIX
  matrix_t A = {};
  matrix_t result = {};
  ck_assert_int_eq(s21_inverse_matrix(&A, &result), ERROR_MATRIX);
}
END_TEST

START_TEST(s21_inverse_matrix_2) {
  // failure when matrix has determinant equal to zero
  matrix_t A = {};
  matrix_t result = {};
  s21_create_matrix(3, 3, &A);
  s21_initialize_matrix(&A, 1, 1);
  ck_assert_int_eq(s21_inverse_matrix(&A, &result), ERROR_CALC);
  s21_remove_matrix(&A);
}
END_TEST

START_TEST(s21_inverse_matrix_3) {
  // failure with non-square matrix
  matrix_t A = {};
  matrix_t result = {};
  s21_create_matrix(5, 3, &A);
  s21_initialize_matrix(&A, 1, 1);
  ck_assert_int_eq(s21_inverse_matrix(&A, &result), ERROR_CALC);
  s21_remove_matrix(&A);
}
END_TEST

START_TEST(s21_inverse_matrix_4) {
  // success matrix 1x1
  matrix_t A = {};
  matrix_t result = {};
  matrix_t eq_matrix = {};
  s21_create_matrix(1, 1, &A);
  s21_create_matrix(1, 1, &eq_matrix);
  A.matrix[0][0] = 21;
  eq_matrix.matrix[0][0] = 1.0 / 21.0;
  ck_assert_int_eq(s21_inverse_matrix(&A, &result), OK);
  ck_assert_int_eq(s21_eq_matrix(&result, &eq_matrix), SUCCESS);
  s21_remove_matrix(&A);
  s21_remove_matrix(&result);
  s21_remove_matrix(&eq_matrix);
}
END_TEST

START_TEST(s21_inverse_matrix_5) {
  // success with task reference values
  matrix_t A = {};
  matrix_t result = {};
  matrix_t eq_matrix = {};
  s21_create_matrix(3, 3, &A);
  s21_create_matrix(3, 3, &eq_matrix);
  A.matrix[0][0] = 2, A.matrix[0][1] = 5, A.matrix[0][2] = 7;
  A.matrix[1][0] = 6, A.matrix[1][1] = 3, A.matrix[1][2] = 4;
  A.matrix[2][0] = 5, A.matrix[2][1] = -2, A.matrix[2][2] = -3;
  eq_matrix.matrix[0][0] = 1, eq_matrix.matrix[0][1] = -1,
  eq_matrix.matrix[0][2] = 1;
  eq_matrix.matrix[1][0] = -38, eq_matrix.matrix[1][1] = 41,
  eq_matrix.matrix[1][2] = -34;
  eq_matrix.matrix[2][0] = 27, eq_matrix.matrix[2][1] = -29,
  eq_matrix.matrix[2][2] = 24;
  ck_assert_int_eq(s21_inverse_matrix(&A, &result), OK);
  ck_assert_int_eq(s21_eq_matrix(&result, &eq_matrix), SUCCESS);
  s21_remove_matrix(&A);
  s21_remove_matrix(&result);
  s21_remove_matrix(&eq_matrix);
}
END_TEST

/*  TEST SUITES */

Suite *s21_create_matrix_suite(void) {
  Suite *suite = suite_create("s21_create_matrix");
  TCase *tc_core = tcase_create("core_of_create_matrix");
  tcase_add_test(tc_core, s21_create_matrix_1);
  tcase_add_test(tc_core, s21_create_matrix_2);
  tcase_add_test(tc_core, s21_create_matrix_3);
  suite_add_tcase(suite, tc_core);

  return suite;
}

Suite *s21_remove_matrix_suite(void) {
  Suite *suite = suite_create("s21_remove_matrix");
  TCase *tc_core = tcase_create("core_of_remove_matrix");
  tcase_add_test(tc_core, s21_remove_matrix_1);
  tcase_add_test(tc_core, s21_remove_matrix_2);
  tcase_add_test(tc_core, s21_remove_matrix_3);
  suite_add_tcase(suite, tc_core);

  return suite;
}

Suite *s21_eq_matrix_suite(void) {
  Suite *suite = suite_create("s21_eq_matrix");
  TCase *tc_core = tcase_create("core_of_eq_matrix");
  tcase_add_test(tc_core, s21_eq_matrix_1);
  tcase_add_test(tc_core, s21_eq_matrix_2);
  tcase_add_test(tc_core, s21_eq_matrix_3);
  tcase_add_test(tc_core, s21_eq_matrix_4);
  tcase_add_test(tc_core, s21_eq_matrix_5);
  tcase_add_test(tc_core, s21_eq_matrix_6);
  suite_add_tcase(suite, tc_core);

  return suite;
}

Suite *s21_sum_matrix_suite(void) {
  Suite *suite = suite_create("s21_sum_matrix");
  TCase *tc_core = tcase_create("core_of_sum_matrix");
  tcase_add_test(tc_core, s21_sum_matrix_1);
  tcase_add_test(tc_core, s21_sum_matrix_2);
  tcase_add_test(tc_core, s21_sum_matrix_3);
  tcase_add_test(tc_core, s21_sum_matrix_4);
  tcase_add_test(tc_core, s21_sum_matrix_5);
  tcase_add_test(tc_core, s21_sum_matrix_6);
  suite_add_tcase(suite, tc_core);

  return suite;
}

Suite *s21_sub_matrix_suite(void) {
  Suite *suite = suite_create("s21_sub_matrix");
  TCase *tc_core = tcase_create("core_of_sub_matrix");
  tcase_add_test(tc_core, s21_sub_matrix_1);
  tcase_add_test(tc_core, s21_sub_matrix_2);
  tcase_add_test(tc_core, s21_sub_matrix_3);
  tcase_add_test(tc_core, s21_sub_matrix_4);
  tcase_add_test(tc_core, s21_sub_matrix_5);
  tcase_add_test(tc_core, s21_sub_matrix_6);
  suite_add_tcase(suite, tc_core);

  return suite;
}

Suite *s21_mult_number_suite(void) {
  Suite *suite = suite_create("s21_mult_number");
  TCase *tc_core = tcase_create("core_of_mult_number");
  tcase_add_test(tc_core, s21_mult_number_1);
  tcase_add_test(tc_core, s21_mult_number_2);
  tcase_add_test(tc_core, s21_mult_number_3);
  tcase_add_test(tc_core, s21_mult_number_4);
  tcase_add_test(tc_core, s21_mult_number_5);
  tcase_add_test(tc_core, s21_mult_number_6);
  suite_add_tcase(suite, tc_core);

  return suite;
}

Suite *s21_mult_matrix_suite(void) {
  Suite *suite = suite_create("s21_mult_matrix");
  TCase *tc_core = tcase_create("core_of_mult_matrix");
  tcase_add_test(tc_core, s21_mult_matrix_1);
  tcase_add_test(tc_core, s21_mult_matrix_2);
  tcase_add_test(tc_core, s21_mult_matrix_3);
  tcase_add_test(tc_core, s21_mult_matrix_4);
  tcase_add_test(tc_core, s21_mult_matrix_5);
  tcase_add_test(tc_core, s21_mult_matrix_6);
  suite_add_tcase(suite, tc_core);

  return suite;
}

Suite *s21_transpose_suite(void) {
  Suite *suite = suite_create("s21_transpose");
  TCase *tc_core = tcase_create("core_of_transpose");
  tcase_add_test(tc_core, s21_transpose_1);
  tcase_add_test(tc_core, s21_transpose_2);
  tcase_add_test(tc_core, s21_transpose_3);
  tcase_add_test(tc_core, s21_transpose_4);
  tcase_add_test(tc_core, s21_transpose_5);
  suite_add_tcase(suite, tc_core);

  return suite;
}

Suite *s21_calc_complements_suite(void) {
  Suite *suite = suite_create("s21_calc_complements");
  TCase *tc_core = tcase_create("core_of_calc_complements");
  tcase_add_test(tc_core, s21_calc_complements_1);
  tcase_add_test(tc_core, s21_calc_complements_2);
  tcase_add_test(tc_core, s21_calc_complements_3);
  suite_add_tcase(suite, tc_core);

  return suite;
}

Suite *s21_determinant_suite(void) {
  Suite *suite = suite_create("s21_determinant");
  TCase *tc_core = tcase_create("core_of_determinant");
  tcase_add_test(tc_core, s21_determinant_1);
  tcase_add_test(tc_core, s21_determinant_2);
  tcase_add_test(tc_core, s21_determinant_3);
  tcase_add_test(tc_core, s21_determinant_4);
  tcase_add_test(tc_core, s21_determinant_5);
  tcase_add_test(tc_core, s21_determinant_6);
  suite_add_tcase(suite, tc_core);

  return suite;
}

Suite *s21_inverse_matrix_suite(void) {
  Suite *suite = suite_create("s21_inverse_matrix");
  TCase *tc_core = tcase_create("core_of_inverse_matrix");
  tcase_add_test(tc_core, s21_inverse_matrix_1);
  tcase_add_test(tc_core, s21_inverse_matrix_2);
  tcase_add_test(tc_core, s21_inverse_matrix_3);
  tcase_add_test(tc_core, s21_inverse_matrix_4);
  tcase_add_test(tc_core, s21_inverse_matrix_5);

  suite_add_tcase(suite, tc_core);

  return suite;
}

/*  SUITES EXECUTION SCRIPT */

void s21_suit_execution(Suite *suite, int *failed_count, char *suite_name) {
  SRunner *suite_runner = srunner_create(suite);
  srunner_set_log(suite_runner, suite_name);
  srunner_run_all(suite_runner, CK_NORMAL);
  *failed_count = srunner_ntests_failed(suite_runner);
  srunner_free(suite_runner);
}

/*  TESTS MAIN */

int main(void) {
  int failed_count = 0;

  s21_suit_execution(s21_create_matrix_suite(), &failed_count,
                     "tests/create_matrix.log");
  s21_suit_execution(s21_remove_matrix_suite(), &failed_count,
                     "tests/remove_matrix.log");
  s21_suit_execution(s21_eq_matrix_suite(), &failed_count,
                     "tests/eq_matrix.log");
  s21_suit_execution(s21_sum_matrix_suite(), &failed_count,
                     "tests/sum_matrix.log");
  s21_suit_execution(s21_sub_matrix_suite(), &failed_count,
                     "tests/sub_matrix.log");
  s21_suit_execution(s21_mult_number_suite(), &failed_count,
                     "tests/mult_number.log");
  s21_suit_execution(s21_mult_matrix_suite(), &failed_count,
                     "tests/mult_matrix.log");
  s21_suit_execution(s21_transpose_suite(), &failed_count,
                     "tests/transpose.log");
  s21_suit_execution(s21_calc_complements_suite(), &failed_count,
                     "tests/calc_complements.log");
  s21_suit_execution(s21_determinant_suite(), &failed_count,
                     "tests/determinant.log");
  s21_suit_execution(s21_inverse_matrix_suite(), &failed_count,
                     "tests/inverse_matrix.log");

  return failed_count == 0 ? EXIT_SUCCESS : EXIT_FAILURE;
}

```

### Makefile

```makefile
CC = gcc -Wall -Werror -Wextra -std=c11
OS = $(shell uname)

ifeq ($(OS),Linux)
    Flags = -lcheck  -lm -lrt -lpthread -lsubunit
else
    Flags = -lcheck -lm -lpthread
endif

all: gcov_report

s21_matrix.a: clean
	$(CC) -c *.c $(Flags)
	ar rcs s21_matrix.a *.o
	ranlib s21_matrix.a
	rm *.o

test: s21_matrix.a
	$(CC) --coverage *.c tests/*.c -o test $(Flags)
	./test

gcov_report: test
	lcov -t "result" -o res.info -c -d .
	genhtml -o report res.info
	open report/index.html

clean:
	rm -rf *.a *.o test *.gcno *.gcda report *.info
	rm -rf tests/*.log
	rm -rf app

clang:
	clang-format -style=Google -n *.c *.h tests/*.c 

clang_i:
	clang-format -style=Google -i *.c *.h tests/*.c

app:
	gcc *c $(Flags) -o app
	./app
```


