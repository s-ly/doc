# Операции с битами

### Распечатать число int в битах

В начале вычисляем поличество бит в числе. Потом циклом перебераем каждый бит. Начиная со старшего, самого большого, левого. Постепенно доходя до младшего правого. Каждый раз сдвигая биты вправо мы подставляем в крайнее правое положение новый бит. Потом применяем & вместе с 1. У еденици только одна еденица справа. Если сдвинутый в право до конца бит равен 1 то он совмещается с 1 и даёи еденицу после операции &. Эта еденица печатается, иначе печатается ноль.

```c
#include <stdio.h>

void print_bits(int number);

int main() {
    int number =  10; // Пример числа для вывода битов
    print_bits(number);
    return  0;
}

void print_bits(int number) {
    
    int caunt_bytes;
    int caunt_bits;
    caunt_bytes = sizeof(number); // число байт в числе
    caunt_bits = caunt_bytes * 8; // число битов в числе
    
    // Перебираем биты числа, начиная с самого старшего бита
    for (int i = caunt_bits - 1; i >=  0; --i) {
        int num_right;
        int num_and_one;
        num_right = number >> i;
        num_and_one = num_right & 1;

        // Выводим бит числа
        printf("%d", num_and_one);
        if (i == 8 || i == 16 || i == 24) printf(" "); // пробелы между байтами
    }
    printf("\n");
}
```

### Сумма двух целых чисел

```c
int sum_bits(int x, int y)
{
    int sum = 0;
    int carry = 0; // перенос

    int caunt_bytes;
    int caunt_bits;
    caunt_bytes = sizeof(x);      // число байт в числе
    caunt_bits = caunt_bytes * 8; // число битов в числе

    for (int i = 0; i < caunt_bits; ++i)
    {
        int bit_a = (x >> i) &  1;
        int bit_b = (y >> i) &  1;
        int bit_sum = bit_a ^ bit_b ^ carry;

        // Обновляем сумму и перенос
        sum |= (bit_sum << i);
        carry = (bit_a & bit_b) | (bit_b & carry) | (carry & bit_a);
    }

    return sum;
}
```


