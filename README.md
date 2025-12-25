<img width="272" height="193" alt="image" src="https://github.com/user-attachments/assets/410f21e1-dbb3-4bc3-aa62-f199a888a6cf" /># Домашнее задание к работе 22
## Условие задачи
2. Вычисляет сумму максимального и минимального значений функции.

## 1. Алгоритм и блок-схема
## Алгоритм
```
1. Начало программы.
2. Подключить библиотеки и определить константы и структуры (SCREENW, SCREENH, Point, TFunc).
3. Определить функции S(x), V(x), Y(x).
4. Реализовать функцию вывода массива точек (printPoints).
5. Реализовать функцию построения графика (plot).
6. Реализовать функцию выбора функции пользователем (selectFunction).
7. Реализовать функции работы с файлами:
   - writeToFile — запись значений функции в файл.
   - readFromFile — чтение значений из файла и вычисление функции.
8. Реализовать табулирование функции (tabulate).
9. Реализовать нахождение максимума методом золотого сечения (goldenSectionMax).
10. Реализовать вычисление суммы максимума и минимума функции (sumMaxMin).
11. Создать главное меню программы:
    - Выбор действий: вычисление функции, табулирование, операции с файлами и максимум, построение графика, сумма максимума и минимума, выход.
    - Для выбранного действия вызвать соответствующую функцию.
12. Повторять меню до выбора выхода.
13. Завершить программу.


```
   
### Блок-схема
1)goldenSectionMax
<img width="230" height="519" alt="image" src="https://github.com/user-attachments/assets/760c0507-a446-4524-b6b4-6b903c28c3d5" />
2) printPoints
<img width="272" height="193" alt="image" src="https://github.com/user-attachments/assets/0ec72573-11f1-4fb2-9190-59be597b8477" />
3)readFromFile
<img width="283" height="470" alt="image" src="https://github.com/user-attachments/assets/c16a3ac7-ee2f-4b3c-938f-b0c6d8c1d7b1" />
4)TFunc selectFunction
<img width="414" height="355" alt="image" src="https://github.com/user-attachments/assets/e9d6a665-2f74-4ae3-87e4-7e9a73389337" />
5)sumMaxMin
<img width="307" height="674" alt="image" src="https://github.com/user-attachments/assets/68ceeaf1-3e1a-43f9-9a44-236a7ab2a751" />
6)tabulate
<img width="280" height="279" alt="image" src="https://github.com/user-attachments/assets/ecd8e836-0ab0-4f3e-aecb-95f352831a94" />
7) writeToFile
<img width="271" height="528" alt="image" src="https://github.com/user-attachments/assets/67893127-5199-4fc2-b657-5a80b777ca8f" />





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
Выбор: 2
Выберите функцию:
1 - S(x)
2 - V(x)
3 - Y(x)
Ваш выбор: 2
Введите интервал x0 x1 и шаг: 0,1 1 0,25
x=0,100000, f(x)=0,099833
x=0,350000, f(x)=0,342898
x=0,600000, f(x)=0,564642
x=0,850000, f(x)=0,751280

МЕНЮ:
1 - Вычислить значение функции
2 - Табулировать функцию
3 - Выполнить операцию
4 - Построить график
5 - Сумма максимума и минимума функции
6 - Выход
Выбор: 3
Выберите функцию:
1 - S(x)
2 - V(x)
3 - Y(x)
Ваш выбор: 3
Операции:
1 - Записать в файл
2 - Вычислить из файла
3 - Найти максимум
Выбор: 3
Введите интервал a b: 0,34 0,8
Максимум f(x) на интервале [0,340000,0,800000] достигается в x=0,340015, f(x)=0,890822

МЕНЮ:
1 - Вычислить значение функции
2 - Табулировать функцию
3 - Выполнить операцию
4 - Построить график
5 - Сумма максимума и минимума функции
6 - Выход
Выбор: 5
Выберите функцию:
1 - S(x)
2 - V(x)
3 - Y(x)
Ваш выбор: 1
Введите интервал x0 x1 и шаг: 0,1
0,2
0,08
Минимум = 0,010000, Максимум = 0,032400
Сумма максимума и минимума = 0,042400

МЕНЮ:
1 - Вычислить значение функции
2 - Табулировать функцию
3 - Выполнить операцию
4 - Построить график
5 - Сумма максимума и минимума функции
6 - Выход
Выбор:



```


<img width="718" height="998" alt="image" src="https://github.com/user-attachments/assets/a04708b5-0330-406b-b6a6-e4395748efa7" />
<img width="1056" height="906" alt="image" src="https://github.com/user-attachments/assets/e1237fd2-b79c-4f35-a22f-6a368563cab0" />
