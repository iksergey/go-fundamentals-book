---
title: Нулевые значения (Zero Values)
description: "Автоматическая инициализация в Go: zero values для всех типов, безопасность памяти без мусора, int=0, string='', bool=false, указатели=nil, отличия от C/Pascal, гарантии компилятора."
keywords: "zero values go, нулевые значения golang, автоматическая инициализация go, nil vs zero value, память безопасность go, default values golang, инициализация переменных"
lang: ru
parent: Базовые типы и переменные
nav_order: 13
has_toc: false
---

# Нулевые значения (Zero Values)

**Нулевые значения переменных и безопасная инициализация в Go**

## Краткое описание

В этом уроке изучаем концепцию нулевых значений (zero values) в Go: какие значения автоматически получают неинициализированные переменные, почему это безопаснее чем в C/Pascal, и как Go гарантирует отсутствие мусора в памяти. Разбираем дефолтные значения для всех базовых типов данных.

## Ключевые концепции

### Автоматическая инициализация

В Go **каждая переменная автоматически инициализируется** нулевым значением её типа, если значение не указано явно:

```go
var age int        // автоматически = 0
var name string    // автоматически = ""
var isActive bool  // автоматически = false
```

**Важно:** в памяти не остаётся "мусора" — Go гарантирует безопасную инициализацию.

### Отличие от C и Pascal

**В C/Pascal:**
```c
int x;  // содержит случайное значение из памяти!
printf("%d", x);  // неопределённое поведение
```

Просто выделяется кусок памяти, который не очищается. Там может быть что угодно.

**В Go:**
```go
var x int  // гарантированно = 0
fmt.Println(x)  // безопасно, выведет 0
```

Go **всегда** инициализирует переменные безопасным значением.

## Нулевые значения базовых типов

### Целочисленные типы → 0

Все целочисленные типы инициализируются нулём:

```go
var intVar int           // 0
var int8Var int8         // 0
var int16Var int16       // 0
var int32Var int32       // 0
var int64Var int64       // 0
var uintVar uint         // 0
var uint8Var uint8       // 0
var byteVar byte         // 0 (альяс uint8)
var runeVar rune         // 0 (альяс int32)
```

**Вывод:**
```
int: 0
int8: 0
int16: 0
int32: 0
int64: 0
uint: 0
byte: 0
rune: 0
```

### Числа с плавающей точкой → 0.0

```go
var float32Var float32   // 0.0
var float64Var float64   // 0.0

fmt.Printf("float32: %.1f\n", float32Var)  // 0.0
fmt.Printf("float64: %.1f\n", float64Var)  // 0.0
```

Не `NaN` или `undefined`, а именно `0.0`.

### Комплексные числа → 0+0i

```go
var complex64Var complex64     // (0+0i)
var complex128Var complex128   // (0+0i)

fmt.Printf("complex64: %v\n", complex64Var)    // (0+0i)
fmt.Printf("complex128: %v\n", complex128Var)  // (0+0i)
```

Действительная часть = 0, мнимая часть = 0.

### Логический тип → false

```go
var boolVar bool  // false

fmt.Printf("bool: %t\n", boolVar)  // false
```

**Не `null`, не `nil`, а именно `false`.**

### Строки → пустая строка ""

```go
var stringVar string  // ""

fmt.Printf("string: '%s'\n", stringVar)      // ''
fmt.Printf("длина: %d\n", len(stringVar))    // 0
```

**Важное отличие:** в некоторых языках (JavaScript, Python с `None`, C# с `null`) строка может быть `null`/`nil`. **В Go строка по умолчанию — пустая строка `""`**, а не `nil`.

### Указатели → nil

```go
var pointerVar *int
var pointerToString *string

fmt.Printf("*int: %v\n", pointerVar)               // <nil>
fmt.Printf("nil? %t\n", pointerVar == nil)         // true

fmt.Printf("*string: %v\n", pointerToString)       // <nil>
fmt.Printf("nil? %t\n", pointerToString == nil)    // true
```

Указатели инициализируются значением `nil` (не указывают ни на что).

## Сложные типы (кратко)

Помимо базовых типов, есть ещё сложные структуры данных, у которых тоже есть нулевые значения:

- **Слайсы** → `nil`
- **Мапы** → `nil`
- **Каналы** → `nil`
- **Функции** → `nil`
- **Интерфейсы** → `nil`
- **Структуры** → все поля инициализируются своими zero values

О них подробнее будет в отдельных уроках.

## Таблица нулевых значений

| Тип | Zero Value | Примечание |
|-----|------------|------------|
| `int`, `int8`, `int16`, `int32`, `int64` | `0` | Все целочисленные |
| `uint`, `uint8`, `uint16`, `uint32`, `uint64` | `0` | Беззнаковые целые |
| `byte` | `0` | Альяс `uint8` |
| `rune` | `0` | Альяс `int32` |
| `float32`, `float64` | `0.0` | Вещественные |
| `complex64`, `complex128` | `0+0i` | Комплексные |
| `bool` | `false` | Логический |
| `string` | `""` | Пустая строка (не nil!) |
| `*T` (любой указатель) | `nil` | Не указывает ни на что |
| `[]T` (слайс) | `nil` | Нулевой слайс |
| `map[K]V` | `nil` | Нулевая мапа |
| `chan T` | `nil` | Нулевой канал |
| `func` | `nil` | Нулевая функция |
| `interface{}` | `nil` | Нулевой интерфейс |
| `struct` | все поля = zero | Рекурсивно |

## Практические примеры

### Полный пример из урока

```go
package main

import "fmt"

func main() {
    fmt.Println("=== Zero Values в Go ===")

    // Целочисленные типы
    var intVar int
    var int8Var int8
    var byteVar byte
    var runeVar rune

    fmt.Println("--- Целочисленные ---")
    fmt.Printf("int: %d\n", intVar)      // 0
    fmt.Printf("int8: %d\n", int8Var)    // 0
    fmt.Printf("byte: %d\n", byteVar)    // 0
    fmt.Printf("rune: %d\n", runeVar)    // 0

    // Вещественные числа
    var float32Var float32
    var float64Var float64

    fmt.Println("\n--- Вещественные ---")
    fmt.Printf("float32: %.1f\n", float32Var)  // 0.0
    fmt.Printf("float64: %.1f\n", float64Var)  // 0.0

    // Комплексные
    var complex64Var complex64
    var complex128Var complex128

    fmt.Println("\n--- Комплексные ---")
    fmt.Printf("complex64: %v\n", complex64Var)     // (0+0i)
    fmt.Printf("complex128: %v\n", complex128Var)   // (0+0i)

    // Логический
    var boolVar bool
    fmt.Println("\n--- Логический ---")
    fmt.Printf("bool: %t\n", boolVar)  // false

    // Строка
    var stringVar string
    fmt.Println("\n--- Строка ---")
    fmt.Printf("string: '%s' (длина: %d)\n", stringVar, len(stringVar))
    // string: '' (длина: 0)

    // Указатели
    var pointerVar *int
    fmt.Println("\n--- Указатели ---")
    fmt.Printf("*int: %v (nil? %t)\n", pointerVar, pointerVar == nil)
    // *int: <nil> (nil? true)
}
```

### Безопасное использование

**Можно смело использовать неинициализированные переменные:**

```go
var counter int
counter++  // безопасно, counter был 0, стал 1

var message string
message += "Hello"  // безопасно, добавляет к пустой строке
```

**С указателями нужна осторожность:**

```go
var ptr *int
// *ptr = 10  // ПАНИКА! nil pointer dereference

// Правильно - сначала инициализировать
var value int = 42
ptr = &value
*ptr = 10  // теперь безопасно
```

## Отличие от других языков

### C#

```csharp
int x;           // 0 (value type)
string s;        // null (reference type)
bool b;          // false
```

В C# строки — ссылочный тип, поэтому `null` по умолчанию.

### JavaScript

```javascript
let x;              // undefined
let y = null;       // null (явно)
let z = 0;          // 0 (явно)
```

JavaScript имеет `undefined` и `null`.

### Python

```python
x = None           # нет неинициализированных переменных
```

Python требует явной инициализации.

### Go

```go
var x int          // 0 (гарантированно)
var s string       // "" (гарантированно)
var b bool         // false (гарантированно)
```

**Go всегда инициализирует безопасным значением, без `null`/`undefined`/`None`.**

## Важные моменты

**1. Гарантия безопасности**
Go гарантирует, что переменная всегда имеет определённое значение. Нет случайного мусора из памяти.

**2. Нет null для примитивов**
В Go нет понятия `null` для базовых типов. Строка всегда существует (может быть пустой), bool всегда `true` или `false`.

**3. nil только для ссылочных типов**
`nil` существует для указателей, слайсов, мап, каналов, функций и интерфейсов — но не для `int`, `string`, `bool`.

**4. Проверка на nil для указателей**
Всегда проверяйте указатели перед разыменованием:

```go
if ptr != nil {
    value := *ptr
}
```

**5. Пустая строка ≠ nil**
```go
var s string       // s == ""
s == ""            // true
s == nil           // ОШИБКА компиляции! string не может быть nil
```

**6. Zero value полезен для счётчиков**
```go
var count int  // 0 автоматически
for i := 0; i < 10; i++ {
    count++
}
```

**7. Структуры инициализируются рекурсивно**
```go
type User struct {
    Name string  // ""
    Age  int     // 0
}

var u User  // Name = "", Age = 0
```

**8. Явная инициализация предпочтительнее**
Хотя zero values безопасны, в коде лучше явно показывать намерения:

```go
// Менее понятно
var count int

// Более понятно (явное намерение)
var count int = 0
```

## Best Practices

### 1. Полагайтесь на zero values для простых типов

```go
var counter int     // 0 - подходит для счётчика
var isReady bool    // false - подходит для флага
```

### 2. Явно инициализируйте, если zero value не подходит

```go
var retries int = 3        // явно не 0
var status string = "new"  // явно не пустая строка
```

### 3. Проверяйте nil для указателей

```go
var ptr *User
if ptr != nil {
    fmt.Println(ptr.Name)
}
```

### 4. Используйте композитные литералы для структур

```go
// Вместо
var u User
u.Name = "Иван"
u.Age = 25

// Лучше
u := User{Name: "Иван", Age: 25}
```

## Что запомнить

- Go **автоматически инициализирует** все переменные нулевыми значениями
- Нет "мусора" в памяти (в отличие от C/Pascal)
- Целые числа: `0`
- Вещественные: `0.0`
- Комплексные: `0+0i`
- Булевы: `false`
- Строки: `""` (пустая строка, **не nil**)
- Указатели: `nil`
- Слайсы, мапы, каналы, функции, интерфейсы: `nil`
- Структуры: все поля = их zero values
- `nil` существует только для ссылочных типов
- Всегда безопасно использовать неинициализированные переменные (кроме указателей)
- Проверяйте `!= nil` перед разыменованием указателей
- Zero values делают код проще и безопаснее
- Явная инициализация часто улучшает читаемость

## Полезные ссылки

- [Go Specification — The zero value](https://go.dev/ref/spec#The_zero_value)
- [Effective Go — Allocation with new](https://go.dev/doc/effective_go#allocation_new)
- [Go Tour — Zero values](https://go.dev/tour/basics/12)
- [Go by Example — Zero Values](https://gobyexample.com/variables)

