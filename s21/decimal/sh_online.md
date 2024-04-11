# Онлайн C#

![CS_online.png](_img_decimal_link/CS_online.png)
[Online C# Compiler - online editor](https://www.onlinegdb.com/online_csharp_compiler)

### Работа с Децималом

```csharp
using System;
public class TestGen
{
  protected static void ConsoleWriteVar (decimal variable, string nameVar)
  {
    var tmp = decimal.GetBits (variable);
      Console.WriteLine ("s21_decimal {4} = {{ {0}, {1}, {2}, {3} }};",
                         tmp[0], tmp[1], tmp[2], tmp[3], nameVar);
  }
  public static void Main (string[]args)
  {
    decimal num_1 = -1034953869456;
    ConsoleWriteVar (num_1, "num_1");
    decimal num_2 = 333425;
    ConsoleWriteVar (num_2, "num_2");
    decimal orig_res = num_1 * num_2;
    ConsoleWriteVar (orig_res, "orig_res");
    ConsoleWriteVar (0, "s21_res");
    Console.WriteLine ("int return_s21 = s21_mul(num_1, num_2, &s21_res);");
    for (int i = 0; i < 4; ++i)
      {
        Console.
          WriteLine
          ("ck_assert_int_eq(s21_res.bits[{0}], orig_res.bits[{0}]);", i);
      }
    Console.WriteLine ("ck_assert_int_eq(return_s21, 0);");
  }
}
```

```csharp
using System;

class Program
{
  static void Main ()
  {
    decimal dividend = 10.5m;
    decimal divisor = 2.5m;

    decimal result = dividend / divisor;

      Console.WriteLine
      (" dividend {0} divisor {1} = {2}", dividend, divisor, result);
  }
}
```

### Побитыво вывожу decimal:

```csharp
using System;

public class TestGen
{
    protected static void ConsoleWriteVar(decimal variable, string nameVar)
    {
        var tmp = decimal.GetBits(variable);
        Console.WriteLine("s21_decimal {4} = {{ {0}, {1}, {2}, {3} }};",
                          tmp[0], tmp[1], tmp[2], tmp[3], nameVar);
    }

    protected static void PrintBits(int number)
    {
        // Преобразование числа в двоичную строку
        string binaryString = Convert.ToString(number, 2);
        // Вывод двоичной строки
        Console.WriteLine(binaryString);
    }

    public static void Main(string[] args)
    {
        decimal num_1 = -34;
        ConsoleWriteVar(num_1, "num_1");

        // Получение значения tmp[0] из num_1
        var tmp = decimal.GetBits(num_1);
        // Вызов функции для вывода битов tmp[0]
        PrintBits(tmp[0]);
        PrintBits(tmp[1]);
        PrintBits(tmp[2]);
        PrintBits(tmp[3]);
    }
}
```

Вывод (число -34):

```powershell
s21_decimal num_1 = { 34, 0, 0, -2147483648 };
100010
0
0
10000000000000000000000000000000
```

### Совместно вычисление и вывод:

```csharp
using System;

class Program
{

  protected static void ConsoleWriteVar (decimal variable, string nameVar)
  {
    var tmp = decimal.GetBits (variable);
      Console.WriteLine ("s21_decimal {4} = {{ {0}, {1}, {2}, {3} }};",
                         tmp[0], tmp[1], tmp[2], tmp[3], nameVar);
  }

  protected static void PrintBits (int number)
  {

    string binaryString = Convert.ToString (number, 2);
    Console.WriteLine (binaryString);
  }


  static void Main ()
  {
    decimal dividend = 10.5m;
    decimal divisor = 2.5m;

    decimal result = dividend / divisor;

    Console.WriteLine
      (" dividend {0} divisor {1} = {2}", dividend, divisor, result);


      // Получение значения tmp[0] из num_1
        var tmp = decimal.GetBits(result);
        // Вызов функции для вывода битов tmp[0]
        PrintBits(tmp[0]);
        PrintBits(tmp[1]);
        PrintBits(tmp[2]);
        PrintBits(tmp[3]);
  }
}
```
