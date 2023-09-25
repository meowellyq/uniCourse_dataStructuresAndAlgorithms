## Переменные

*Переменная* представляет собой место в памяти с именем.
Компьютер выделяет (предоставляет) это место в памяти, когда выполняется наша программа.

### Типы

У каждой переменной есть связанный с ней *тип*. 
На данный момент думайте о типе как о концептуальном имени, которое определяет количество байтов памяти,
которые нужно будет выделить для переменной.
Например, тип `std::byte` означает, что этот размер будет равен 1 байту памяти, 
`int` обычно 4 байта, 
и `size_t` обычно 8 байтов.

### Декларация

Чтобы **объявить** переменную (заставить компьютер выделить память для неё и дать этой памяти имя в исходном коде),
нам нужно указать тип и имя переменной.
Например:

cpp
// Объявляем int, то есть выделяем 4 байта памяти и называем это "i"
int i;
// Объявляем байт, то есть выделяем 1 байт памяти и называем это "myByte"
std::byte myByte;
```

### Инициализация

Вам также нужно инициализировать переменную, то есть записать значение в память этой переменной.
По умолчанию память любой переменной "неинициализирована", что означает, что она будет содержать то, что было в этой памяти ранее, что на практике может быть чем угодно
(это часто называется "мусором").
Обычно то, что вы делаете, это сразу "обнуляете" значение, если собираетесь использовать его позже.
Обнуление значения означает запись нуля в каждый байт памяти переменной.
В C++ вы делаете это, присваивая `{ }` (вы также можете использовать 0, если это числовой тип, например, int).

### Определение

Объявление и инициализация переменной вместе также называется определением переменной.
Но поскольку переменные инициализируются по умолчанию (с мусором, но все же фактически инициализированы),
любое объявление переменной также является ее определением.

```cpp
// Определяем int и заполняем все 4 байта нулями.
int i = 0;
// То же самое
int number = { };

// Определяем int и устанавливаем 4 байта, чтобы содержать 42.
// Это корректно установит битовый паттерн 4 байтов, чтобы представлять 42 в двоичной системе.
// Значение каждого байта в десятичной системе: 0 0 0 42
// И теперь это в двоичной системе: 00000000 00000000 00000000 00101010
int someNumber = 42;

// Объявляем int, затем инициализируем его отдельно.
// Между объявлением и инициализацией переменная будет содержать мусорные данные.
// ... Декларация
int g;
// ... Инициализация
g = 10;
```

### Присваивание

Инициализация обычно относится к первому разу, когда вы записываете значение в переменную.
Но в принципе вы можете перезаписать значение переменной столько раз, сколько захотите.
Это называется **присваиванием**, и действие установки значения называется **присвоить**.

```cpp
// Определяем несколько чисел
int a = 5;
int b = 10;
int c = 20;

// Определяем переменную для хранения суммы, инициализированную нулем.
int sum = 0;
// Добавляем значение каждой переменной к сумме.
sum = sum + a; // 0 + 5 = 5
sum = sum + b; // 5 + 10 = 15
sum = sum + c; // 15 + 20 = 35
```

## Массивы

Массив - это непрерывный блок памяти, где каждый элемент массива имеет одинаковый тип
(т.е. занимает одинаковое количество байтов).
Вы можете думать о массивах как о группе переменных, где каждая переменная названа своим индексом в массиве.

Чтобы визуально понять, как это размещено в памяти, посмотрите на [memory_example_1](./memory_example_1).

```cpp
// Объявляет массив из 5 int, то есть 5 * 4 байта = 20 байтов.
// Это фактически означает, что мы объявляем 5 переменных типа int,
// и они имеют имена array[0], array[1], array[2], array[3], array[4].
int array[5];

std::byte array[2];
```

### Индексация массива

Массивы индексируются, начиная с 0, поэтому первый элемент - это `array[0]`, а не `array[1]`.
Считайте индекс как смещение, измеренное в размере типа каждого элемента, от начала памяти массива.

В таблице ASCII ниже представлены первые 12 байтов памяти массива `int`.

```
address:          0 1 2 3   4 5 6 7   8 9 10 11 ... 
bytes graphic:  | _ _ _ _ | _ _ _ _ | _ _ _ _ | ...
offset (index): 0         1         2         3 ...
```

Вы можете использовать элемент массива, например `array[0]`, как любую другую обычную переменную: присваивать ему значение, читать из него, обнулять его, присваивая `{ }`.

Но одна из наиболее полезных вещей с массивами заключается в том, что вы можете использовать не постоянный индекс.
Например:

```cpp
int array[3] = { };

size_t index = 2;
// Устанавливает array[2] равным 69.
array[index] = 69;

size_t otherIndex = 1;
// Устанавливает array[1] равным 420.
array[otherIndex] = 420;
```

Это может быть полезно, когда вы получаете индекс откуда-то еще, например, от пользовательского ввода или просто другой функции.


## Указатели

> [Видео](https://www.youtube.com/watch?v=2ybLD6_2gKM)

Если вы понимаете адреса памяти, то вы в основном понимаете указатели.
Указатель - это переменная, которая хранит адрес памяти.
Для примера того, как указатель хранится в памяти, посмотрите [memory_example_1](./memory_example_1).

```cpp
int someNumber = 1;
// Объявляет указатель, который имеет адрес памяти `someNumber`.
// Оператор & называется "адрес" и он возвращает адрес памяти переменной, на которую он указывает.
int* pointerToSomeNumber = &someNumber;
```

### Размеры, адреса и `size_t`

Тип указателя определяет, к какому типу переменной указывает адрес, однако этот тип не влияет на размер самого адреса.
Адрес переменной любого типа всегда имеет одинаковый размер, который составляет 8 байтов (64 бита) на 64-битных архитектурах или 4 байта (32 бита) на 32-битных архитектурах.

> Технически это может быть чем угодно, но в 99,9% случаев это одно из этих двух.
> Для дальнейшего обсуждения предположим, что это 8 байтов.

Особый тип `size_t` указывает максимальную длину в байтах блока памяти, что по смыслу также означает максимально возможный адрес, потому что каждый байт памяти имеет адрес, и адрес последнего байта этого блока памяти является максимально возможным адресом и равен `size - 1`.
Таким образом, `size_t` - это тип, который может хранить любой размер (в байтах) блока памяти или любой адрес. Обычно он используется для индексов или длин массивов.

Мы можем получить числовое значение адреса, преобразовав его в `size_t`. Преобразование из одного типа в другой называется "приведение типа".

```cpp
int someNumber = 1;
int* pointerToSomeNumber = &someNumber;
// Преобразует указатель в size_t, интерпретируя адрес памяти в нем как число.
size_t addressOfSomeNumber = (size_t) pointerToSomeNumber;
```

### Разыменовывание

"Разыменовывание" означает получение значения по адресу, хранящемуся в указателе.

Другими словами, это означает:

1. Получите адрес, сохраненный в памяти переменной указателя;
2. Перейдите к этому адресу в памяти;
3. Прочитайте значение по этому адресу.

```cpp
int value = 0;
int* pointerToValue = &value;

// Разыменовывает указатель, т.е. получает значение по адресу, хранящемуся в указателе.
// `valueFromPointer` будет равен 0.
int valueFromPointer = *pointerToValue;

value = 10;

// Разыменовывает указатель снова, т.е. получает значение по адресу, хранящемуся в указателе.
// `valueFromPointer` останется равным 0 (потому что это копия ранее сохраненного значения, сохраненного в памяти valueFromPointer)
// но `otherValueFromPointer` будет равен 10 (как прочитано из `value`).
int otherValueFromPointer = *pointerToValue;
```

### Запись по адресу памяти

Эта операция отличается от разыменовывания, хотя у неё и одинаковый синтаксис.
Это означает запись значения в память по адресу, который хранится в указателе.

Другими словами:

1. Получите адрес, сохраненный в памяти переменной указателя;
2. Запишите по этому адресу в памяти.

```cpp
int value = 0;
int* pointerToValue = &value;

// `value` становится равным 10.
*pointerToValue = 10;
```

### Указатели в указателях

Указатели могут указывать на другие указатели, бесконечно.
Указатель на указатель означает, что первый указатель 
хранит адрес памяти, где находится память другого указателя,
то есть где он хранит адрес переменной, на которую указывает.

Они иногда полезны, но редко используются в исходном виде.
Двойное или даже тройное разыменовывание (разыменовывание указателей несколько раз в программе)
очень распространено, но обычно это скрыто под более понятной абстракцией.
Сырые указатели на указатели трудно понимать. Если вы так считаете, знайте, что это нормально.

```cpp
int value = 0;
int otherValue = 5;
int* pointerToValue = &value;
int** pointerToPointerToValue = &pointerToValue;

// Мы можем получить значение `pointerToValue`, то есть адрес, сохраненный в его памяти,
// разыменовав `pointerToPointerToValue`.
int* anotherPointerToValue = *pointerToPointerToValue;

// Теперь мы можем записать в `value`, записав в `*anotherPointerToValue`.
// Таким образом, `value` устанавливается равным 10.
*anotherPointerToValue = 10;

// Теперь пусть `pointerToValue` указывает на `otherValue`.
pointerToValue = &otherValue;

// Это дает 5.
int valueOfOtherValue = *pointerToValue;

// Но у этого адреса все еще старый адрес `value`, поэтому он читает 10.
int valueOfValue = *anotherPointerToValue;

// Но двойное разыменовывание `pointerToPointerToValue` дает нам новый адрес `otherValue`.
int valueOfOtherValueAgain = **pointerToPointerToValue;
```

> Время вопроса: сколько байтов занимает тип int**?

<details>
<summary>Ответ</summary>
`size_t` (обычно 8 байт), так же как и любой другой адрес памяти.
</details>

### `void*`

Тип `void*` - это особый тип указателя, который может указывать на любой тип переменной.

```cpp
int value = 0;
std::byte otherValue = 5;

// Сохраните адрес `value` в `void*`.
void* pointer = &value;

// Эта строка не скомпилируется (void* нельзя разыменовывать).
// int valueFromPointer = *pointer;

// Но мы можем привести его к int* сначала, а затем разыменовать, что будет работать.
int* typedPointer = (int*) pointer;
int valueFromPointer = *typedPointer;
// или как одна операция
int valueFromPointer = * (int*) pointer;
```

### Указатели на массивы и арифметика указателей

> [Видео](https://youtu.be/q24-QTbKQS8).
> Это на C, поэтому синтаксис немного отличается со структурами.

Так же, как и с любой другой переменной, вы можете указать указатель на элемент в массиве.

```cpp
int array[3] = { 1, 2, 3 };

// Укажите на первый элемент массива.
int* pointerToArray = &array[0];
// Укажите на второй элемент массива.
int* pointerToArray = &array[1];

int i = 2;
// Указатель на какой-то i-й элемент массива.
int* pointerToArray = &array[i];
```

Вы также можете добавлять и вычитать числа из указателей для навигации по блокам памяти (как массивы),
с увеличением размера типа указателя.

```cpp
int array[3] = { 1, 2, 3 };

// Укажите на первый элемент массива.
int* pointerToArray = &array[0];
int value1 = *pointerToArray; // 1

// Перейдите к второму элементу.
// Обратите внимание, что +1 меняет адрес не на один байт, а на 4 байта в этом случае.
pointerToArray = pointerToArray + 1;
int value2 = *pointerToArray; // 2

// Вернитесь к первому элементу.
pointerToArray = pointerToArray - 1;
int value3 = *pointerToArray; // 1

// Перейдите к третьему элементу, пропустив второй.
pointerToArray = pointerToArray + 2;
int value4 = *pointerToArray; // 3
```

`void*` действует как `std::byte*` при выполнении арифметики указателей, то есть навигация в памяти осуществляется с приращением 1 байта.

Еще одно замечание: вы можете вычитать два указателя, чтобы узнать количество элементов между ними.

> `ptrdiff_t` - это знаковый вариант `size_t`.

```cpp
int array[3] = { 1, 2, 3 };
int* pointerToArray0 = &array[0];
int* pointerToArray2 = &array[2];
ptrdiff_t numberOfItems = pointerToArray2 - pointerToArray0; // 2
ptrdiff_t negativeNumberOfItems = pointerToArray0 - pointerToArray2; // -2
```

Если вы хотите узнать разницу в байтах между двумя указателями, вы можете привести их к `std::byte*` или `size_t` или `ptrdiff_t` сначала.
Также вы не можете вычитать два `void*` или указатели разных типов, если вы их сначала не приведете.

> Лучший способ, вероятно, `std::byte*`, потому что это не вызовет никаких странностей со знаками.
> При вычитании `size_t` вы должны быть уверены, что левый больше, 
> а приведение к `ptrdiff_t` теряет один бит информации (знаковый бит). Вычитание типов, отличных от `std::byte*` для указателей, очень быстро переходит в область 
> "разрешено ли переполнение или недополнение для этого типа, или это неопределенное поведение?",
> которого лучше просто избегать.

```cpp
int array[3] = { 1, 2, 3 };
int* pointerToArray0 = &array[0];
int* pointerToArray2 = &array[2];

size_t numberOfBytes = (size_t) pointerToArray2 - (size_t) pointerToArray0; // 8
// или
ptrdiff_t numberOfBytes = (ptrdiff_t) pointerToArray2 - (ptrdiff_t) pointerToArray0; // 8
// или
ptrdiff_t numberOfBytes = (std::byte*) pointerToArray2 - (std::byte*) pointerToArray0; // 8
```

Добавление двух указателей также не разрешено 
(если подумать, действительно не совсем понятно, зачем это делать).

> Обратите внимание, что вычитание указателей разрешено стандартом C++ 
> только если они указывают на элементы в одном и том же массиве / блоке памяти.
> В противном случае это неопределенное поведение (что угодно может случиться, не полагайтесь на это).

## Функции

Функции - это блоки кода, которые можно выполнять из других мест в программе.
В терминологии инструкций функция - это последовательность инструкций, 
у которых есть адрес, с которого они начинаются в исполняемом файле.
Чтобы прояснить, каждая инструкция в исполняемых файлах имеет адрес, который является
таким же, как и память, позицией инструкции в файле, с некоторым базовым смещением (скажем, 100).
Это смещение может варьироваться при различных запусках программы, но относительные позиции инструкций
в файле всегда будут одинаковыми.

> TODO: кому-то следует проверить это, я просто передаю свое понимание этого,
> могу ошибаться в деталях.

Когда вы вызываете функцию в коде, процессор "переходит" к адресу первой инструкции
функции и начинает выполнять инструкции оттуда.
Когда функция завершает выполнение, процессор "переходит"
назад к инструкции после той, что вызвала функцию.

Выполнение функции называется "вызовом" или "вызовом" функции,
и возвращение из функции после завершения выполнения называется "возвращением" из функции.
Говорят, что вызванная функция "возвращает".

### Функция `main`

Функция с именем `main` - это особая функция, которая вызывается автоматически при запуске программы.
Это называется точкой входа программы.

> Пока не беспокойтесь о `include`, `int` или `return 0;`.

```cpp
#include <iostream>
int main()
{
    // Печатает "Hello" на консоли при выполнении.
    std::cout << "Hello";
    return 0;
}
```

> `return 0` указывает на успех, возвращение любого другого значения указывает на ошибку.


### Прототипы

**Прототип**, также называемый сигнатурой в других языках, - это описание интерфейса функции.
Он описывает, какие "параметры" или входные данные принимает функция, и что она "возвращает" или выводит.

Вы можете думать о функции на высоком уровне как о заводе:

- Он принимает что-то (параметры), например, сталь и пластик;
- Он выполняет с ним некоторые операции;
- В результате получается что-то другое (возвращаемое значение), например, автомобиль.

Вы, вероятно, знакомы с математической нотацией \( f(x, y, z) \rightarrow \mathbb{Z} \), которая говорит:

- Функция называется `f`;
- Она принимает параметры `x`, `y` и `z`;
- Она возвращает значение, которое является целым числом (\( \mathbb{Z} \)).

Обратите внимание, что в этой математической нотации типы параметров часто подразумеваются контекстом,
но их также можно указать явно отдельно,
например, здесь: \( f(x, y) \rightarrow \mathbb{Z}, x \in \mathbb{Z}, y \in \mathbb{Z} \).

То же самое происходит в C++.
Любая функция имеет возвращаемый тип, имя и список параметров, где у каждого из них есть имя и тип.

Например, если мы аппроксимируем \( \mathbb{Z} \) как `int`, мы можем объявить эту функцию в C++ так:

```cpp
int f(int x, int y);
```

Это называется прототипом функции или сигнатурой функции.

Здесь мы объявляем функцию, которая возвращает какой-то указатель и принимает указатель на int и int.

```cpp
void* getSomePointer(int* pointer, int someInt);
```

### Процедуры

Процедурой называется функция, которая не имеет возвращаемого типа
или не возвращает ничего в обычном смысле слова.
Тем не менее, она может влиять на память вызывающего через свои параметры,
например, изменяя значение с использованием переданного в качестве параметра указателя.
Также она может производить видимые побочные эффекты, такие как печать на консоли.

Вы обозначаете возвращаемый тип процедуры как `void`, чтобы указать "нет возвращаемого типа".

```cpp
// Вот процедура, которая просто печатает hello на консоли.
// (производит побочный эффект)
void printHello()
{
    std::cout << "Hello";
}

// Вот процедура, которая принимает указатель на int
// и записывает в него значение.
// (влияет на память вызывающего)
void writeValue(int* pointer)
{
    *pointer = 10;
}
```

### Объявление функции

Функции аналогичны переменным, в том что у них есть объявление и определение.
Однако, в отличие от переменных, функции не инициализируются мусором по умолчанию, как переменные,
они просто непригодны в качестве только объявления, если определение также предоставлено где-то в программе.

Например, следующая программа не будет компилироваться из-за ошибки компоновщика,
поскольку у `add` отсутствует ее определение.

```cpp
int add(int a, int b);

int main()
{
    int c = add(1, 2);
    return 0;
}
```

### Определение функции

Определением функции является фактическая реализация функции.
Это также называется телом функции.

В приведенном выше примере, если бы мы определили тело `add` (какие инструкции она должна выполнять),
программа была бы скомпилирована и запущена корректно.

```cpp
// объявление
int add(int a, int b);

// определение
int add(int a, int b)
{
    return a + b;
}

int main()
{
    int c = add(1, 2);
    return 0;
}
```

Мы можем поместить определение в любом месте программы.

> При условии, что есть только одно определение.
> Может случиться, что у вас есть несколько определений одной и той же функции,
> в случаях, когда вы определяете одну и ту же функцию в нескольких исходных файлах,
> и затем пытаетесь связать их вместе в один исполняемый файл.
> Это завершится ошибкой компоновщика, указывающей на наличие более чем одного определения.

```cpp
// объявление
int add(int a, int b);

int main()
{
    int c = add(1, 2);
    return 0;
}

// определение
// В этом случае оно размещено после его вызова в файле.
int add(int a, int b)
{
    return a + b;
}
```

Ограничением является то, что определение должно предшествовать вызову функции.
Следующее *не будет компилироваться*:

```cpp
int main()
{
    int c = add(1, 2);
    return 0;
}

// объявление
int add(int a, int b);
```

Вы также можете объединить объявление с определением, 
аналогично тому, как вы делаете это с переменными:

```cpp
// объявление + определение
int add(int a, int b)
{
    return a + b;
}

int main()
{
    int c = add(1, 2);
    return 0;
}
```

### Локальные переменные размещаются в стеке

*вставьте объяснение здесь*, вздох.

Пример [example 3](./memory_example_3) показывает, как работает разные виды памяти.

### Рекурсия (рекурсивные функции)

*вставьте объяснение здесь*, вздох.

Пример [example 4](./memory_example_4) показывает, как работает рекурсия.

## Структуры

Посмотрите [memory_example_2](./memory_example_2) для примера того, как структуры размещены в памяти.

Структура - это средство для группировки нескольких переменных.
Формально структура - это пользовательский тип данных, который может определить программист.
Переменные, объявленные в структуре, называются полями.

Ниже мы объявляем структуру, которая группирует две переменные типа int с именами `a` и `b`.

```cpp
struct TwoInts
{
    int a;
    int b;
};
```

И ниже мы объявляем структуру "Car", чтобы хранить данные о машине.

```cpp
struct Car
{
    Color color; // другая пользовательская структура, определенная где-то еще
    int numberOfDoors; // переменная типа int
    int wheelLastChangedYear[4]; // массив из 4 переменных типа int
};
```

### Использование и оператор доступа к полю

Для использования структуры вам нужно объявить переменную этого типа.
Затем вы можете получить доступ к полям структуры, используя оператор `.`.

```cpp
struct Point
{
    int x;
    int y;
};

int main()
{
    Point point;
    point.x = 10;
    point.y = 20;
    
    // 10 + 20 = 30
    int a = point.x + point.y;
    
    return 0;
}
```

Структура в целом имеет имя `point`, а переменные внутри 
имеют имена `point.x` и `point.y` (их имя в объявлении, с префиксом `point.`).
Думайте об операторе `.` как о доступе к памяти всего point,
и выборе нужной вам переменной.

### Копирование структур

Таким же образом, как у вас может быть несколько переменных одного типа,
у вас может быть несколько переменных одного и того же типа, например, `Point`.

```cpp
struct Point
{
    int x;
    int y;
};

int main()
{
    Point point1;
    point1.x = 10;
    point1.y = 20;
    
    Point point2;
    point2.x = 30;
    point2.y = 40;
    
    // 10 + 20 = 30
    int a = point1.x + point1.y;
    // 30 + 40 = 70
    int b = point2.x + point2.y;
    
    return 0;
}
```

Вы также можете полностью копировать точки друг в друга.
Это означает копирование всех байтов, что будет
устанавливать переменные в одном на их значения в другом.

```cpp
struct Point
{
    int x;
    int y;
};

int main()
{
    Point point1;
    point1.x = 10;
    point1.y = 20;
    
    Point point2;
    point2.x = 30;
    point2.y = 40;
    
    // Копировать point1 в point2.
    // устанавливает point2.x в 10
    // устанавливает point2.y в 20
    point2 = point1;
    
    // 10 + 20 = 30
    int a = point1.x + point1.y;
    // 10 + 20 = 30
    int b = point2.x + point2.y;
    
    return 0;
}
```

### Указатели на структуры и оператор `->`

Также можно иметь указатели на структуры,
так же, как у вас могут быть указатели на любой другой тип.

Можно использовать оператор `->` для разыменования указателя на структуру,
а затем получить доступ к одному из полей.
Можно думать о `->x` как "перейти к адресу указателя, затем взять локальную переменную `x`".

```cpp
struct Point
{
    int x;
    int y;
};

int main()
{
    Point point;
    point.x = 10;
    point.y = 20;
    
    Point* pointerToPoint = &point;
    pointerToPoint->x = 30;
    
    // 30 + 20 = 50
    int a = point.x + point.y;
    
    return 0;
}
```

### Массивы структур

Вы также можете иметь массивы структур.

```cpp
struct Point
{
    int x;
    int y;
};

int main()
{
    Point points[3] = { };
    points[0].x = 10;
    points[1].x = 20;
    points[2].y = 30;
    
    // 10 + 20 + 30 = 60
    int a = points[0].x + points[1].x + points[2].y;
    
    return 0;
}
```

### `sizeof`

Оператор [`sizeof`](https://en.cppreference.com/w/cpp/language/sizeof) возвращает размер типа в байтах.
Это полезно, например, при выделении памяти с помощью malloc.