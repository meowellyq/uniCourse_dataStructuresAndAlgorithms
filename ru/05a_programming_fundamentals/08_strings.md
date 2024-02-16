# Строки

*Строки* - это массивы символов.

## ASCII

Как представить символ в памяти (т.е. как число)?
Для этого изобрели *кодировки*. 
Самая простая кодировка - 7-битная кодировка, называемая ASCII.
Символы и их значения можете глянуть, например, [здесь](https://www.asciitable.com/).

Обратите внимание, что любой символ из ASCII можно представить 7 битами,
поэтому в байте можно хранить не более одного символа.
Конечно, старший (high) бит всегда равен 0, 
чтобы значение байта было эквивалентно значению из таблицы ASCII.

Тип, представляющий один символ ASCII, - это `char`.
`char` - это 8-битный тип, который может быть как со знаком, 
так и без, в зависимости от компилятора.

```cpp
#include <iostream>

int main()
{
    char character = 65; // 65 - код A в таблице ASCII
    std::cout << character; // выводит A
}
```

Также можно использовать *литералы символов*, чтобы компилятор сам находил
число в таблице ASCII за вас.

```cpp
char character = 'A';
// Эквивалентно:
char character = 65;
```

Можно выполнять арифметические операции с символами, например,
увеличивать их или добавлять к ним смещение.
Теперь внимательно посмотрите на заглавные латинские буквы в таблице ASCII.
Они идут подряд.
Это означает, что мы можем вывести весь алфавит, используя цикл.

```cpp
char startCharacter = 'A';
char endCharacter = 'Z';
for (char ch = startCharacter; ch <= endCharacter; ch++)
    std::cout << ch;
```

То же самое относится и к маленьким буквам.
Например, можно вычислить смещение между началом заглавных букв и началом маленьких букв,
чтобы преобразовывать буквы из верхнего регистра в нижний.

> Рекомендую использовать дебаггер для лучшего понимания идеи.

```cpp
char offset = 'a' - 'A';
for (char ch = 'A'; ch <= 'Z'; ch++)
{
    char result = ch + offset;
    std::cout << result;
}

// Или наоборот

char offset = 'A' - 'a';
for (char ch = 'a'; ch <= 'z'; ch++)
{
    char result = ch + offset;
    std::cout << result;
}
```

## Unicode

Unicode относится к стандартизированному набору кодировок,
которые можно использовать для кодирования любого символа.
Самой популярной из них является UTF-8.

Все обычные символы ASCII представлены в UTF-8 без изменений.
Проблема заключается в том, что у нас нет больше места в байте для представления других символов,
кроме одного неиспользуемого бита ASCII.
Идея заключается в том, что ты можем использовать этот бит, чтобы указать,
что символ закодирован в нескольких байтах.

Например, символ `ș` можно записать двумя байтами,
например, как `C8 99` в шестнадцатеричной системе.
UTF-8 использует старшие биты для указания количества байт, в которых закодирован символ.
- Первый бит, когда равен `1`, означает, что *текущий символ содержится в нескольких байтах*.
- Второй бит после `1`, когда равен `0`, означает, что *текущий байт не является первым байтом
  символа*; в противном случае он является.
- Количество `1` перед `0`, следующих за первой `1`, включая первую `1`,
  указывает *количество байт символа*.

Идея заключается в том, что любой байт содержит контекст, то есть знает,
является ли он самостоятельным байтом ASCII (первый бит равен 0),
первым байтом последовательности (паттерн 11),
или одним из других байтов в последовательности (паттерн 10).

В таблице ниже показан этот паттерн:

| Количество байт  | Байт 1   | Байт 2   | Байт 3   | Байт 4   |
|------------------|----------|----------|----------|----------|
| 1 (просто ASCII) | 0....... | N/A      | N/A      | N/A      |
| 2                | 110..... | 10...... | N/A      | N/A      |
| 3                | 1110.... | 10...... | 10...... | N/A      |
| 4                | 11110... | 10...... | 10...... | 10...... |

4 байта - это максимум.
Если символ кодируется более чем 4 байтами (это случай для большинства emoji),
его называют кластером графем (grapheme cluster).
Он тогда представлен несколькими символами UTF-8.

> Различная терминология может вызвать путаницу, поэтому термин "символ" в
> UTF-8 фактически бессмысленен.
> Лучше обращаться к символу (одной вещи, которая появляется в тексте)
> как *текстовому элементу*, например, чтобы избежать путаницы.
> Различные источники / реализации выбирают различную терминологию.

> Символ, который помещается в 4 байта, иногда называют *руна*.

Существуют также UTF-16 и UTF-32, у которых минимальное количество байт для
символа увеличено до 2 и 4 соответственно, и они используют похожие идеи для кодирования.

## C-строки

*C-строка* - это последовательность символов, заканчивающаяся *нулевым завершителем*,
т.е. числом 0.
В C (не C++!) строки обычно хранятся массивом или как указатель,
без хранения длины строки (количества байтов).
Программа предполагает, что где бы строка ни заканчивалась, в конце будет байт 0.
Таким образом она находит конец строки или ее длину.

```cpp
#include <iostream>
#include <array>

int main()
{
    std::array<char, 6> arr{ 
        'H',
        'e',
        'l',
        'l',
        'o',
        '\0', // то же самое, что и просто 0
    }; 

    // `std::cout` обрабатывает `char*` особым образом.
    // Он предполагает, что это C-строка, и печатает ее таким образом.
    std::cout << &arr[0]; // Hello
    return 0;
}
```

И если вы напишете 0 в середине строки,
он выводет только до этого "нового конца".

```cpp
std::array<char, 6> arr{ 
    'H',
    '\0',
    'l',
    'l',
    'o',
    '\0',
}; 
std::cout << &arr[0]; // H
```

Можно использовать строковый литерал вместо перечисления каждого байта:

```cpp
std::array<char, 6> arr = "Hello";
```

Строковые литералы также могут быть присвоены указателю.
Это поместит их в статическую память,
и на некоторых платформах память будет неизменяемой
(запись в нее вызовет крашнет программу).
Вы не должны записывать в память, на которую указывают такие указатели.

```cpp
const char* str = "Hello";
```

## `std::string_view`

Очень часто вам нужно знать длину строки
или сослаться только на часть всей строки,
поэтому полезно хранить длину вместе с указателем.

В этом отношении `std::string_view` работает так же, как `const std::span<char>`.
Он хранит указатель на строку вместе с длиной.
Единственное отличие в том, что у него есть некоторые методы, которые полезны для строк,
и выходные потоки (например, `std::cout`) печатают его как строку.

> Для справки: оператор `<<` не перегружен для `std::span` для потоков,
> что означает, что вы не можете сделать `std::cout << span;`, это просто не скомпилируется.

Также есть различие в конструкторах, в том смысле, что `std::string_view`
не может быть неявно создан из `std::array`.
Однако, он может быть создан из `const char*`,
что заставит его пройтись по всех строке,
пока не найдет нулевой байт, чтобы определить длину.

```cpp
#include <iostream>
#include <array>
#include <string>

int main()
{
    std::array<char, 6> arr = "Hello";
    std::string_view str{&arr[0]};
    std::cout << str.size(); // 5
    std::cout << str; // Hello
    return 0;
}
```

Как и с `std::span`, вы можете передать ему длину в конструкторе:

```cpp
std::array<char, 6> arr = "Hello";
std::string_view str{&arr[0], 2};
std::cout << str; // He
```

## `std::string`

`std::string` - это RAII тип, который позволяет выделять строку в heap.

```cpp
#include <string>
#include <iostream>

int main()
{ 
    // Создание из литерала.
    // Обратите внимание, что строка копируется в буфер, выделенный в heap,
    // из статической памяти, где хранится литерал.
    // Локальная переменная хранит указатель на буфер и длину.
    std::string string1{"Hello world!!"};

    // Конструктор на основе указателя + длина
    // обратите внимание, что он копирует строку в новый буфер, выделенный в heap.
    std::string string2{&string1[0], 10};

    // Это НЕ создает новый буфер.
    // Он указывает на буфер string2.
    std::string_view view{string2};

    std::cout << string1; // Hello world!!
    std::cout << string2; // Hello worl
    std::cout << view; // Hello worl

    return 0;

    // Добавлено неявно:
    // string2.~string()
    // string1.~string()
}
```

### `std::string` можно изменять

```cpp
std::string string1{"Hello world!!"};
string1[0] = 'L';
std::cout << string1; // Lello world!!
```

Он также поддерживает удобные операторы конкатенации:

```cpp
std::string string1{"Hello"};
string1 += " world"; // Выделит новый буфер по необходимости.
std::cout << string1; // Hello world
```

Также, для коротких строк, динамическая память не выделится.
Если строка достаточно короткая, она будет храниться внутри самого объекта 
(обычно до 23 байт, не требуя выделения памяти).
Это называется *оптимизация малых строк* (small string optimization).


### `std::string` в C++ - это динамический массив

`std::string` по сути работает как `std::vector<char>`, потому что ее буфер, capacity и
длина разделены, что позволяет вам потенциально добавлять символы в конец
без выделения памяти при каждом добавлении.

В C#, например, строки являются неизменными,
что означает, что каждый раз, когда вы пытаетесь добавить
символ в конец, создасться новая строка, в нее будет скопирована старая строка и
затем будет добавлен новый символ в конец.
Это неэффективно, но строки C# побеждают в том плане, что они неизменны,
что имеет свои преимущества.


### `const std::string&` параметр -- это глупо

**Не используйте `const std::string&` в качестве типа параметра.**

Если `const` означает, что вы не сможете его изменить, зачем заставлять
пользователя предоставлять строку как `std::string`?
Есть много других способов представления строки, ее можно хранить в любом
линейном блоке памяти.

Лучшим вариантом является замена этого на `std::string_view`, который предназначен
для использования в этом контексте. 
Это гораздо более гибко, потому что вы можете создавать его без выделения динамической памяти
из любой пары указатель + длина.


### `std::string` завершается нулем

Буфер символов любого `std::string` всегда завершен нулем,
что гарантируется стандартом C++.
Для получения указателя на буфер, вы можете использовать метод `c_str()`.