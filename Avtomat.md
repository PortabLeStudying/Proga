# Задачи на автомат на языке C
---
Выполнил Бентелев А.С., ИВТ-1.2

## Задание 1: Проверка сбалансированности скобок в выражении

### Условие
Написать программу для проверки корректности расстановки круглых `()`, фигурных `{}` и квадратных `[]` скобок в заданной строке.

---

### Реализация

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>

#define MAX_SIZE 1024

// Структура стека
typedef struct {
    char data[MAX_SIZE];
    int top;
} Stack;

// Инициализация стека
void init(Stack *s) {
    s->top = -1;
}

// Проверка, пуст ли стек
int isEmpty(Stack *s) {
    return s->top == -1;
}

// Добавление элемента в стек
void push(Stack *s, char c) {
    if (s->top >= MAX_SIZE - 1) {
        printf("Stack overflow\n");
        exit(1);
    }
    s->data[++(s->top)] = c;
}

// Удаление элемента из стека
char pop(Stack *s) {
    if (isEmpty(s)) {
        return '\0';
    }
    return s->data[(s->top)--];
}

// Проверяет, является ли символ открывающей скобкой
int isOpenBracket(char c) {
    return c == '(' || c == '{' || c == '[';
}

// Возвращает соответствующую закрывающую скобку
char getMatchingBracket(char c) {
    switch (c) {
        case '(': return ')';
        case '{': return '}';
        case '[': return ']';
        default: return '\0';
    }
}

// Основная функция проверки
int isBalanced(const char *expr) {
    Stack stack;
    init(&stack);

    for (int i = 0; expr[i]; ++i) {
        char current = expr[i];

        if (isOpenBracket(current)) {
            push(&stack, current); // Добавляем открытую скобку в стек
        } else if (current == ')' || current == '}' || current == ']') {
            if (isEmpty(&stack)) return 0; // Нет парной открытой скобки
            char top = pop(&stack);
            if (getMatchingBracket(top) != current) return 0; // Не совпадает тип скобки
        }
    }

    return isEmpty(&stack); // Все скобки должны быть закрыты
}

int main() {
    char expr[MAX_SIZE];

    printf("Enter the expression: ");
    fgets(expr, MAX_SIZE, stdin);
    expr[strcspn(expr, "\n")] = 0; // Удаляем символ новой строки

    if (isBalanced(expr)) {
        printf("YES\n");
    } else {
        printf("NO\n");
    }

    return 0;
}
```

---

### Результаты работы программы

![](https://github.com/PortabLeW/Proga/blob/images/Avtomat-11.png)

![](https://github.com/PortabLeW/Proga/blob/images/Avtomat-12.png)

![](https://github.com/PortabLeW/Proga/blob/images/Avtomat-13.png)

---

### Список идентификаторов

| Имя                | Тип / Структура     | Назначение                                       |
|--------------------|---------------------|--------------------------------------------------|
| `expr`             | `char[]`            | Входная строка с выражением                     |
| `MAX_SIZE`         | `int` (константа)   | Максимальный размер стека и входной строки      |
| `Stack`            | `struct`            | Структура данных для реализации стека           |
| `init`             | `void`              | Инициализирует стек                             |
| `isEmpty`          | `int`               | Проверяет, пуст ли стек                         |
| `push`             | `void`              | Добавляет элемент в стек                        |
| `pop`              | `char`              | Удаляет и возвращает верхний элемент стека      |
| `isOpenBracket`    | `int`               | Проверяет, является ли символ открывающей скобкой |
| `getMatchingBracket` | `char`             | Возвращает соответствующую закрывающую скобку   |
| `isBalanced`       | `int`               | Основная функция проверки баланса скобок        |

---

### Краткий анализ
Для реализации задачи использовался **массив** как основа стека.  
Алгоритм последовательно обрабатывает входную строку, игнорируя все символы, кроме скобок.  
Каждая открывающая скобка помещается в стек, а при встрече закрывающей — сравнивается с верхним элементом стека.  
Если стек пуст или скобки не соответствуют друг другу — возвращается ошибка.  
Программа корректно обрабатывает разные уровни вложенности и типы скобок.

---

## Задание 2: Вычисление выражения в постфиксной нотации

### Условие
Реализовать программу вычисления арифметического выражения в обратной польской записи.

---

### Реализация

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>

#define MAX_SIZE 1024
#define MAX_STACK 1024

// Стек целых чисел
typedef struct {
    int data[MAX_STACK];
    int top;
} IntStack;

void initIntStack(IntStack *s) {
    s->top = -1;
}

int isEmptyIntStack(IntStack *s) {
    return s->top == -1;
}

void pushInt(IntStack *s, int val) {
    if (s->top >= MAX_STACK - 1) {
        printf("Stack overflow\n");
        exit(1);
    }
    s->data[++(s->top)] = val;
}

int popInt(IntStack *s) {
    if (isEmptyIntStack(s)) {
        printf("Not enough operands\n");
        exit(1);
    }
    return s->data[(s->top)--];
}

// Функция выполнения операции
int applyOp(char op, int a, int b) {
    switch (op) {
        case '+': return a + b;
        case '-': return a - b;
        case '*': return a * b;
        case '/':
            if (b == 0) {
                printf("Division by zero\n");
                exit(1);
            }
            return a / b;
        default:
            printf("Unknown operator: %c\n", op);
            exit(1);
    }
}

// Основная функция
int evaluatePostfix(const char *expr) {
    IntStack stack;
    initIntStack(&stack);

    char *copy = strdup(expr);
    char *token = strtok(copy, " ");

    while (token != NULL) {
        if (isdigit(token[0]) || (token[0] == '-' && strlen(token) > 1)) {
            // Число
            pushInt(&stack, atoi(token));
        } else if (strlen(token) == 1) {
            // Оператор
            int b = popInt(&stack);
            int a = popInt(&stack);
            int result = applyOp(token[0], a, b);
            pushInt(&stack, result);
        } else {
            printf("Invalid token: %s\n", token);
            exit(1);
        }
        token = strtok(NULL, " ");
    }

    if (stack.top != 0) {
        printf("Invalid expression\n");
        exit(1);
    }

    return popInt(&stack);
}

int main() {
    char expr[MAX_SIZE];

    printf("Enter the expression in postfix notation: ");
    fgets(expr, MAX_SIZE, stdin);
    expr[strcspn(expr, "\n")] = 0;

    int result = evaluatePostfix(expr);
    printf("Result: %d\n", result);

    return 0;
}
```

---

### Результаты работы программы

![](https://github.com/PortabLeW/Proga/blob/images/Avtomat-21.png)

![](https://github.com/PortabLeW/Proga/blob/images/Avtomat-22.png)

![](https://github.com/PortabLeW/Proga/blob/images/Avtomat-23.png)

---

### Список идентификаторов

| Имя                | Тип / Структура     | Назначение                                       |
|--------------------|---------------------|--------------------------------------------------|
| `expr`             | `char[]`            | Входная строка с постфиксным выражением         |
| `MAX_SIZE`         | `int` (константа)   | Максимальный размер входной строки              |
| `MAX_STACK`        | `int` (константа)   | Максимальный размер стека                       |
| `IntStack`         | `struct`            | Структура стека целых чисел                     |
| `initIntStack`     | `void`              | Инициализация стека                             |
| `isEmptyIntStack`  | `int`               | Проверяет, пуст ли стек                         |
| `pushInt`          | `void`              | Добавляет число в стек                          |
| `popInt`           | `int`               | Извлекает число из стека                        |
| `applyOp`          | `int`               | Применяет оператор к двум числам                |
| `evaluatePostfix`  | `int`               | Основная функция вычисления выражения           |

---

### Краткий анализ
Задача решена с использованием **реализованного вручную стека**, где хранятся промежуточные результаты вычислений.  
При обработке токенов выражения числа добавляются в стек, а операции извлекают нужное количество операндов, производят вычисление и кладут результат обратно.  
В случае ошибок (например, деление на ноль или недостаточно операндов), программа выводит сообщение и завершает работу.  
Использование динамической обработки строк позволяет корректно разбивать выражение на части.

---

## Задание 3: Генерация треугольника Паскаля

### Условие
Создать программу, выводящую первые N строк треугольника Паскаля.

---

### Реализация

```c
#include <stdio.h>
#include <stdlib.h>

// Функция генерации треугольника Паскаля
void Pascal(int n) {
    int **triangle = (int **)malloc(n * sizeof(int *));
    for (int i = 0; i < n; i++) {
        triangle[i] = (int *)malloc((i + 1) * sizeof(int));
        triangle[i][0] = 1;
        triangle[i][i] = 1;
        for (int j = 1; j < i; j++) {
            triangle[i][j] = triangle[i - 1][j - 1] + triangle[i - 1][j];
        }
    }

    // Вывод
    for (int i = 0; i < n; i++) {
        for (int j = 0; j <= i; j++) {
            printf("%d ", triangle[i][j]);
        }
        printf("\n");

        free(triangle[i]); // Освобождение памяти
    }
    free(triangle);
}

int main() {
    int n;
    printf("Enter the number of lines of Pascal's triangle: ");
    scanf("%d", &n);

    Pascal(n);

    return 0;
}
```

---

### Результаты работы программы

![](https://github.com/PortabLeW/Proga/blob/images/Avtomat-31.png)

![](https://github.com/PortabLeW/Proga/blob/images/Avtomat-32.png)

---

### Список идентификаторов

| Имя                | Тип / Структура     | Назначение                                       |
|--------------------|---------------------|--------------------------------------------------|
| `n`                | `int`               | Количество строк треугольника Паскаля           |
| `triangle`         | `int**`             | Двумерный массив для хранения треугольника      |
| `Pascal` | `void`        | Функция генерации треугольника Паскаля          |
| `i`, `j`           | `int`               | Счётчики циклов                                 |
| `malloc`, `free`   | `функции`           | Выделение и освобождение динамической памяти    |

---

### Краткий анализ
Треугольник Паскаля строится с использованием **двумерного динамического массива**.  
Каждый новый элемент рассчитывается как сумма двух предыдущих из верхней строки.  
Выделенная под каждую строку память освобождается после вывода, что предотвращает утечки.  
Программа работает с любым положительным N, ограниченным только доступной памятью.

---

## Задание 4: Арифметические операции с дробями

### Условие
Разработать программу для выполнения основных операций с обыкновенными дробями и вывод результата в несократимом виде.

---

### Реализация

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// НОД
int gcd(int a, int b) {
    while (b != 0) {
        int temp = b;
        b = a % b;
        a = temp;
    }
    return a;
}

// Обработка дроби
void processFraction(int a_num, int a_den, int b_num, int b_den, char op) {
    int numerator, denominator;

    switch (op) {
        case '+':
            numerator = a_num * b_den + b_num * a_den;
            denominator = a_den * b_den;
            break;
        case '-':
            numerator = a_num * b_den - b_num * a_den;
            denominator = a_den * b_den;
            break;
        case '*':
            numerator = a_num * b_num;
            denominator = a_den * b_den;
            break;
        case '/':
            numerator = a_num * b_den;
            denominator = a_den * b_num;
            break;
        default:
            printf("Unknown operator\n");
            return;
    }

    // Приведение к несократимому виду
    int d = gcd(numerator, denominator);
    numerator /= d;
    denominator /= d;

    // Если знаменатель отрицательный, меняем знак
    if (denominator < 0) {
        numerator *= -1;
        denominator *= -1;
    }

    printf("%d/%d\n", numerator, denominator);
}

int main() {
    int a_num, a_den, b_num, b_den;
    char op;
    char input[100];

    printf("Enter the expression in the format A/B or C/D: ");
    fgets(input, 100, stdin);

    sscanf(input, "%d/%d %c %d/%d", &a_num, &a_den, &op, &b_num, &b_den);

    if (a_den == 0 || b_den == 0) {
        printf("The denominator cannot be zero..\n");
        return 1;
    }

    processFraction(a_num, a_den, b_num, b_den, op);

    return 0;
}
```

---

### Результаты работы программы

![](https://github.com/PortabLeW/Proga/blob/images/Avtomat-41.png)

![](https://github.com/PortabLeW/Proga/blob/images/Avtomat-42.png)

![](https://github.com/PortabLeW/Proga/blob/images/Avtomat-43.png)

---

### Список идентификаторов

| Имя                | Тип                 | Назначение                                       |
|--------------------|---------------------|--------------------------------------------------|
| `a_num`, `a_den`   | `int`               | Числитель и знаменатель первой дроби            |
| `b_num`, `b_den`   | `int`               | Числитель и знаменатель второй дроби            |
| `op`               | `char`              | Оператор (+, -, *, /)                           |
| `gcd`              | `int`               | Находит наибольший общий делитель               |
| `numerator`, `denominator` | `int`       | Результат операции над дробями                  |
| `processFraction`  | `void`              | Обрабатывает операцию и выводит результат       |
| `input`            | `char[]`            | Входная строка с выражением                      |

---

### Краткий анализ
Программа выполняет четыре базовые арифметические операции над дробями.  
Для упрощения результата используется **наибольший общий делитель (НОД)**.  
Учитывается случай отрицательного знаменателя — знак переносится на числитель.  
Ввод обрабатывается через строку с помощью `sscanf`.  
Программа корректно обрабатывает ошибки, такие как деление на ноль.

---

## Вывод
Все задачи выполнены согласно требованиям:  
- Использованы **собственные реализации структур данных** (стек через массив).  
- Для динамических структур применено **динамическое выделение памяти** и её освобождение.  
- Программы **обрабатывают возможные ошибки**: деление на ноль, некорректные данные, нехватка операндов.  
- Все алгоритмы работают за оптимальное время, без лишних действий.
