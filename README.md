# Домашнее задание к работе 17
## Условие задачи
1) сравнение простых сортировок (выбором, пузырьковая, коктельная, вставками) для различных размеров выборок  100, 1000, 10000 значений


## 1. Алгоритм и блок-схема
## Алгоритм
```
1. Начало
2. Задать размеры массивов: 100, 1000 и 10000.
3. Для каждого размера массива выделить память под четыре массива.
4. Заполнить массивы одинаковыми случайными числами.
5. Выполнить пузырьковую сортировку и измерить время работы.
6. Выполнить сортировку выбором и измерить время работы.
7. Выполнить коктейльную сортировку и измерить время работы.
8. Выполнить сортировку вставками и измерить время работы.
9. Вывести в консоль результаты измерений.
10. Освободить выделенную память.
11. Вывести общий вывод и завершить программу.

```
   
### Блок-схема

![Lab17](https://github.com/user-attachments/assets/037e4e83-0bcb-41d9-b618-9b410df0f579)




## 2. Реализация программы

```
#define _CRT_SECURE_NO_WARNINGS
#define _USE_MATH_DEFINES
#include <locale.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h> 
#include <conio.h>
#include <math.h>
#include <time.h>

#define SCREENW 60
#define SCREENH 20

typedef double (*TFunc)(double);

typedef struct {
    double x;
    double y;
} Point;

// Пример функций
double S(double x) { return x * x; }
double V(double x) { return sin(x); }
double Y(double x) { return exp(-x * x); }

// Печать массива точек
void printPoints(Point* arr, int n) {
    for (int i = 0; i < n; i++) {
        printf("x = %lf, y = %lf\n", arr[i].x, arr[i].y);
    }
}

// Построение графика
void plot(double x0, double x1, TFunc f) {
    char screen[SCREENW][SCREENH];
    double x, y[SCREENW];
    double ymin = 0, ymax = 0, hx, hy;
    int i, j, xz, yz;

    hx = (x1 - x0) / (SCREENW - 1);

    for (i = 0, x = x0; i < SCREENW; ++i, x += hx) {
        y[i] = f(x);
        if (i == 0) { ymin = ymax = y[i]; }
        if (y[i] < ymin) ymin = y[i];
        if (y[i] > ymax) ymax = y[i];
    }

    hy = (ymax - ymin) / (SCREENH - 1);
    yz = (int)floor(ymax / hy + 0.5);
    xz = (int)floor((0.0 - x0) / hx + 0.5);

    for (j = 0; j < SCREENH; ++j)
        for (i = 0; i < SCREENW; ++i) {
            if (j == yz && i == xz) screen[i][j] = '+';
            else if (j == yz) screen[i][j] = '-';
            else if (i == xz) screen[i][j] = '|';
            else screen[i][j] = ' ';
        }

    for (i = 0; i < SCREENW; ++i) {
        j = (int)floor((ymax - y[i]) / hy + 0.5);
        if (j >= 0 && j < SCREENH)
            screen[i][j] = '*';
    }

    for (j = 0; j < SCREENH; ++j) {
        for (i = 0; i < SCREENW; ++i)
            putchar(screen[i][j]);
        putchar('\n');
    }
}

// Выбор функции
TFunc selectFunction() {
    int choice;
    printf("Выберите функцию:\n1 - S(x)\n2 - V(x)\n3 - Y(x)\nВаш выбор: ");
    scanf("%d", &choice);
    switch (choice) {
    case 1: return S;
    case 2: return V;
    case 3: return Y;
    default: printf("Неверный выбор, выбрана S(x)\n"); return S;
    }
}

// Запись значений функции в файл
void writeToFile(TFunc f) {
    double x0, x1, step;
    printf("Введите интервал x0 x1 и шаг: ");
    scanf("%lf %lf %lf", &x0, &x1, &step);
    FILE* fp = fopen("dat.txt", "w");
    if (!fp) { printf("Ошибка открытия файла!\n"); return; }
    for (double x = x0; x <= x1; x += step) {
        fprintf(fp, "%lf,%lf\n", x, f(x));
    }
    fclose(fp);
    printf("Данные записаны в dat.txt\n");
}

// Чтение значений из файла и вычисление функции
void readFromFile(TFunc f) {
    FILE* fp = fopen("dat.txt", "r");
    if (!fp) { printf("Ошибка открытия файла!\n"); return; }
    double x;
    char comma;
    while (fscanf(fp, "%lf%c", &x, &comma) == 2) {
        printf("x=%lf, f(x)=%lf\n", x, f(x));
    }
    fclose(fp);
}

// Табулирование функции
void tabulate(TFunc f) {
    double x0, x1, step;
    printf("Введите интервал x0 x1 и шаг: ");
    scanf("%lf %lf %lf", &x0, &x1, &step);
    for (double x = x0; x <= x1; x += step) {
        printf("x=%lf, f(x)=%lf\n", x, f(x));
    }
}

// Максимум методом золотого сечения
double goldenSectionMax(TFunc f, double a, double b, double tol) {
    const double phi = (1 + sqrt(5)) / 2;
    double c = b - (b - a) / phi;
    double d = a + (b - a) / phi;
    while (fabs(c - d) > tol) {
        if (f(c) < f(d)) a = c; else b = d;
        c = b - (b - a) / phi;
        d = a + (b - a) / phi;
    }
    return (b + a) / 2;
}
void sumMaxMin(TFunc f) {
    double x0, x1, step;
    printf("Введите интервал x0 x1 и шаг: ");
    scanf("%lf %lf %lf", &x0, &x1, &step);

    double min = f(x0), max = f(x0);
    for (double x = x0; x <= x1; x += step) {
        double y = f(x);
        if (y < min) min = y;
        if (y > max) max = y;
    }
    printf("Минимум = %lf, Максимум = %lf\n", min, max);
    printf("Сумма максимума и минимума = %lf\n", min + max);
}
// Главное меню
int main() {
    setlocale(LC_ALL, "RUS");

    int choice;
    while (1) {
        printf("\nМЕНЮ:\n");
        printf("1 - Вычислить значение функции\n");
        printf("2 - Табулировать функцию\n");
        printf("3 - Выполнить операцию\n");
        printf("4 - Построить график\n");
        printf("5 - Сумма максимума и минимума функции\n");
        printf("6 - Выход\n");
        printf("Выбор: ");
        scanf("%d", &choice);

        if (choice == 6) break;

        TFunc f;
        double x;
        switch (choice) {
        case 1:
            f = selectFunction();
            printf("Введите x: ");
            scanf("%lf", &x);
            printf("f(x)=%lf\n", f(x));
            break;
        case 2:
            f = selectFunction();
            tabulate(f);
            break;
        case 3:
            f = selectFunction();
            printf("Операции:\n1 - Записать в файл\n2 - Вычислить из файла\n3 - Найти максимум\nВыбор: ");
            int op;
            scanf("%d", &op);
            if (op == 1) writeToFile(f);
            else if (op == 2) readFromFile(f);
            else if (op == 3) {
                double a, b;
                printf("Введите интервал a b: ");
                scanf("%lf %lf", &a, &b);
                double xmax = goldenSectionMax(f, a, b, 1e-5);
                printf("Максимум f(x) на интервале [%lf,%lf] достигается в x=%lf, f(x)=%lf\n", a, b, xmax, f(xmax));
            }
            break;
        case 4:
            f = selectFunction();
            double x0, x1;
            printf("Введите интервал x0 x1: ");
            scanf("%lf %lf", &x0, &x1);
            plot(x0, x1, f);
            break;
        case 5:
            f = selectFunction();
            sumMaxMin(f);
            break;
        default:
            printf("Неверный выбор\n");
        }
    }
    return 0;
}

```


## 3. Результаты работы программы

```
Эксперимент: сравнение алгоритмов сортировки
--------------------------------------------

Размер | Bubble | Select | Cocktail | Insert
-------+--------+--------+----------+--------
   100 | 0,0000 | 0,0000 |   0,0000 | 0,0000
  1000 | 0,0030 | 0,0020 |   0,0020 | 0,0000
 10000 | 0,1240 | 0,0670 |   0,1260 | 0,0600

Выводы по эксперименту:
- пузырьковая сортировка показывает наименьшую скорость;
- коктейльная работает немного эффективнее пузырьковой;
- сортировка выбором даёт средние результаты;
- сортировка вставками оказалась наиболее быстрой.



```


<img width="1116" height="406" alt="{7FCBE7F9-B8C5-41E8-9F76-2DE21E380145}" src="https://github.com/user-attachments/assets/67495d51-feaf-41a2-ab9b-aeb63da48239" />
