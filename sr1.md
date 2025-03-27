# Самостоятельная работа 1

Ниже приведены решения задач на языке C, оформленные в формате Markdown с таблицами значений переменных.

---

## Задача 1. Динамический массив и вычисление среднего арифметического

### Код программы
```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int N;
    float *array, sum = 0.0, average;
    float *ptr;

    printf("Введите количество элементов массива: ");
    scanf("%d", &N);

    array = (float *)malloc(N * sizeof(float));
    if (array == NULL) {
        printf("Ошибка выделения памяти\n");
        return 1;
    }

    printf("Введите %d элементов массива:\n", N);
    for (ptr = array; ptr < array + N; ptr++) {
        scanf("%f", ptr);
    }

    for (ptr = array; ptr < array + N; ptr++) {
        sum += *ptr;
    }

    average = sum / N;

    printf("Среднее арифметическое: %.2f\n", average);

    free(array);

    return 0;
}
```

### Таблица значений переменных

| Переменная | Тип      | Начальное значение | Пример значений после выполнения |
|------------|----------|--------------------|----------------------------------|
| `N`        | `int`    | Неопределено       | 5                                |
| `array`    | `float*` | `NULL`             | Адрес выделенной памяти          |
| `sum`      | `float`  | 0.0                | 15.0 (если элементы: 1.0, 2.0, 3.0, 4.0, 5.0) |
| `average`  | `float`  | Неопределено       | 3.0                              |
| `ptr`      | `float*` | Неопределено       | Указывает на элементы массива     |

---

## Задача 2. Каталог книг

### Код программы
```c
#include <stdio.h>
#include <string.h>

#define MAX_LENGTH 50

typedef struct {
    char title[MAX_LENGTH];
    char author[MAX_LENGTH];
    int year;
} Book;

void printBook(const Book *book) {
    printf("Название: %s", book->title);
    printf("Автор: %s", book->author);
    printf("Год издания: %d\n", book->year);
}

int main() {
    Book books[3];
    int i;
    for (i = 0; i < 3; i++) {
        printf("Введите данные для книги %d:\n", i + 1);

        printf("Название: ");
        fgets(books[i].title, MAX_LENGTH, stdin);
        books[i].title[strcspn(books[i].title, "\n")] = '\0';

        printf("Автор: ");
        fgets(books[i].author, MAX_LENGTH, stdin);
        books[i].author[strcspn(books[i].author, "\n")] = '\0';

        printf("Год издания: ");
        scanf("%d", &books[i].year);
        getchar(); 
    }

    printf("\nИнформация о книгах:\n");
    for (i = 0; i < 3; i++) {
        printBook(&books[i]);
    }

    return 0;
}
```

### Таблица значений переменных

| Переменная | Тип       | Начальное значение | Пример значений после выполнения |
|------------|-----------|--------------------|----------------------------------|
| `books`    | `Book[3]` | Пустые строки      | `{ {"Книга1", "Автор1", 2020}, {"Книга2", "Автор2", 2015}, {"Книга3", "Автор3", 2010} }` |
| `i`        | `int`     | Неопределено       | 0, 1, 2                          |

---

## Задача 3. Определение сезона по номеру месяца

### Код программы
```c
#include <stdio.h>

typedef enum {
    WINTER,
    SPRING,
    SUMMER,
    AUTUMN
} Season;

int main() {
    int month;
    Season season;

    printf("Введите номер месяца (1-12): ");
    scanf("%d", &month);

    switch (month) {
        case 12:
        case 1:
        case 2:
            season = WINTER;
            break;
        case 3:
        case 4:
        case 5:
            season = SPRING;
            break;
        case 6:
        case 7:
        case 8:
            season = SUMMER;
            break;
        case 9:
        case 10:
        case 11:
            season = AUTUMN;
            break;
        default:
            printf("Неверный номер месяца\n");
            return 1;
    }

    // Вывод сезона
    const char *seasonNames[] = {"Зима", "Весна", "Лето", "Осень"};
    printf("Сезон: %s\n", seasonNames[season]);

    return 0;
}
```

### Таблица значений переменных

| Переменная | Тип      | Начальное значение | Пример значений после выполнения |
|------------|----------|--------------------|----------------------------------|
| `month`    | `int`    | Неопределено       | 7                                |
| `season`   | `Season` | Неопределено       | `SUMMER`                         |

---

### Комментарии к коду

- **Задача 1**: Используется арифметика указателей для обхода массива.
- **Задача 2**: Функция `printBook()` принимает указатель на структуру и выводит её поля.
- **Задача 3**: Перечисление `Season` используется для удобного представления сезонов.

### Заключение

Программы написаны с учетом требований заданий и демонстрируют работу с динамической памятью, структурами и перечислениями.
