---
title: Полный обзор типов данных
description: "Все базовые типы Go: знаковые и беззнаковые целые int8-int64, uint8-uint64, float32/64, complex64/128, специальные типы byte и rune, работа с Unicode символами и форматирование вывода."
keywords: "типы данных go, int golang, uint типы, float32 float64, byte rune golang, unicode go, комплексные числа go, форматирование printf go"
lang: ru
parent: Базовые типы и переменные
nav_order: 5
has_toc: false
---

# Полный обзор типов данных: от int до rune

## Краткое описание

В этом уроке делаем полный практический обзор всех базовых типов данных в Go: знаковые и беззнаковые целые числа всех размеров, числа с плавающей точкой, комплексные числа, логический и строковый типы. Разбираем специальные типы-альясы `byte` и `rune`, учимся работать с Unicode символами.

## Ключевые концепции

### Синтаксис объявления через var

В Go переменные объявляются с использованием ключевого слова `var`:

```go
var имя тип = значение
```

Этот подход называется **полным объявлением** — явно указываем тип данных. В последующих уроках изучим короткие формы объявления.

### Все целочисленные типы на практике

**Знаковые целые (signed):**

```go
var intVar int = 100                      // зависит от архитектуры
var int8Var int8 = 127                    // -128 до 127
var int16Var int16 = 32767                // -32768 до 32767
var int32Var int32 = 2147483647           // ~-2 млрд до ~2 млрд
var int64Var int64 = 9223372036854775807  // ~-9 квинтиллионов
```

**Беззнаковые целые (unsigned):**

```go
var uintVar uint = 100                        // зависит от архитектуры
var uint8Var uint8 = 255                      // 0 до 255
var uint16Var uint16 = 65535                  // 0 до 65535
var uint32Var uint32 = 4294967295             // 0 до ~4 млрд
var uint64Var uint64 = 18446744073709551615   // 0 до ~18 квинтиллионов
```

### Числа с плавающей точкой

```go
var float32Var float32 = 3.14159
var float64Var float64 = 2.718281828459045

fmt.Printf("float32: %.5f (тип: %T)\n", float32Var, float32Var)
fmt.Printf("float64: %.15f (тип: %T)\n", float64Var, float64Var)
```

**Разница в точности:**
- `float32` — до 6-7 значащих цифр
- `float64` — до 15-16 значащих цифр

### Комплексные числа

Go имеет встроенную поддержку комплексных чисел (редко используются в бизнес-приложениях):

```go
var complex64Var complex64 = 1 + 2i
var complex128Var complex128 = 3 + 4i

fmt.Printf("complex64: %v (тип: %T)\n", complex64Var, complex64Var)
fmt.Printf("complex128: %v (тип: %T)\n", complex128Var, complex128Var)
```

**Формат:** `действительная_часть + мнимая_частьi`

**Практическое применение:** научные вычисления, обработка сигналов, математическое моделирование. В типичной backend-разработке (REST API, работа с JSON, базы данных) почти не встречаются.

### Специальные типы: byte и rune

**byte — альяс для uint8:**

```go
var byteVar byte = 255
fmt.Printf("byte: %d (тип: %T)\n", byteVar, byteVar)
// Вывод: byte: 255 (тип: uint8)
```

Важно: при выводе типа через `%T` вы увидите `uint8`, а не `byte` — это один и тот же тип.

**Использование byte:**
- Работа с сырыми данными (raw data)
- Чтение/запись файлов
- Сетевые протоколы
- Манипуляции с бинарными данными

**rune — альяс для int32:**

```go
var runeVar rune = 'Я'
fmt.Printf("rune: %c (Unicode: U+%04X, тип: %T)\n", runeVar, runeVar, runeVar)
// Вывод: rune: Я (Unicode: U+042F, тип: int32)
```

При выводе типа через `%T` увидите `int32` — это альяс.

**Использование rune:**
- Представление одного Unicode символа
- Итерация по символам строки
- Работа с многобайтовыми символами (кириллица, иероглифы, эмодзи)

### Шаблоны форматирования

**%v — универсальный вывод:**

```go
fmt.Printf("%v\n", complex64Var)  // вывод в формате по умолчанию
```

Шаблон `%v` работает с любым типом данных — выводит значение в стандартном формате.

**%c — символ из rune:**

```go
var r rune = 'A'
fmt.Printf("%c\n", r)  // A
```

**%X — шестнадцатеричное представление:**

```go
var r rune = 'Я'
fmt.Printf("U+%04X\n", r)  // U+042F (Unicode code point)
```

## Практика

### Полный рабочий пример

```go
package main

import "fmt"

func main() {
    fmt.Println("=== Базовые типы данных в Go ===")

    // Целочисленные типы
    var intVar int = 100
    var int8Var int8 = 127
    var int16Var int16 = 32767
    var int32Var int32 = 2147483647
    var int64Var int64 = 9223372036854775807

    fmt.Println("--- Целочисленные типы ---")
    fmt.Printf("int: %d (тип: %T)\n", intVar, intVar)
    fmt.Printf("int8: %d (тип: %T)\n", int8Var, int8Var)
    fmt.Printf("int16: %d (тип: %T)\n", int16Var, int16Var)
    fmt.Printf("int32: %d (тип: %T)\n", int32Var, int32Var)
    fmt.Printf("int64: %d (тип: %T)\n", int64Var, int64Var)

    // Беззнаковые целые
    var uintVar uint = 100
    var uint8Var uint8 = 255
    var uint16Var uint16 = 65535
    var uint32Var uint32 = 4294967295
    var uint64Var uint64 = 18446744073709551615

    fmt.Println("\n--- Беззнаковые целочисленные типы ---")
    fmt.Printf("uint: %d (тип: %T)\n", uintVar, uintVar)
    fmt.Printf("uint8: %d (тип: %T)\n", uint8Var, uint8Var)
    fmt.Printf("uint16: %d (тип: %T)\n", uint16Var, uint16Var)
    fmt.Printf("uint32: %d (тип: %T)\n", uint32Var, uint32Var)
    fmt.Printf("uint64: %d (тип: %T)\n", uint64Var, uint64Var)

    // Числа с плавающей точкой
    var float32Var float32 = 3.14159
    var float64Var float64 = 2.718281828459045

    fmt.Println("\n--- Числа с плавающей точкой ---")
    fmt.Printf("float32: %.5f (тип: %T)\n", float32Var, float32Var)
    fmt.Printf("float64: %.15f (тип: %T)\n", float64Var, float64Var)

    // Комплексные числа
    var complex64Var complex64 = 1 + 2i
    var complex128Var complex128 = 3 + 4i

    fmt.Println("\n--- Комплексные числа ---")
    fmt.Printf("complex64: %v (тип: %T)\n", complex64Var, complex64Var)
    fmt.Printf("complex128: %v (тип: %T)\n", complex128Var, complex128Var)

    // Логический тип
    var boolVar bool = true

    fmt.Println("\n--- Логический тип ---")
    fmt.Printf("bool: %t (тип: %T)\n", boolVar, boolVar)

    // Строковый тип
    var stringVar string = "Привет, Go!"

    fmt.Println("\n--- Строковый тип ---")
    fmt.Printf("string: %s (тип: %T)\n", stringVar, stringVar)

    // Специальные целочисленные типы
    var byteVar byte = 255
    var runeVar rune = 'Я'

    fmt.Println("\n--- Специальные типы ---")
    fmt.Printf("byte: %d (тип: %T)\n", byteVar, byteVar)
    fmt.Printf("rune: %c (Unicode: U+%04X, тип: %T)\n", runeVar, runeVar, runeVar)
}
```

### Разбор вывода программы

```
=== Базовые типы данных в Go ===
--- Целочисленные типы ---
int: 100 (тип: int)
int8: 127 (тип: int8)
...
--- Специальные типы ---
byte: 255 (тип: uint8)      ← обратите внимание: тип uint8!
rune: Я (Unicode: U+042F, тип: int32)  ← тип int32!
```

### Работа с Unicode через rune

```go
// Латинский символ
var latin rune = 'A'
fmt.Printf("%c = U+%04X\n", latin, latin)
// Вывод: A = U+0041

// Кириллица
var cyrillic rune = 'Я'
fmt.Printf("%c = U+%04X\n", cyrillic, cyrillic)
// Вывод: Я = U+042F

// Китайский иероглиф
var chinese rune = '你'
fmt.Printf("%c = U+%04X\n", chinese, chinese)
// Вывод: 你 = U+4F60

// Эмодзи
var emoji rune = '🚀'
fmt.Printf("%c = U+%04X\n", emoji, emoji)
// Вывод: 🚀 = U+1F680
```

## Важные моменты

**1. Альясы — это не новые типы**
`byte` и `rune` — просто удобные имена для `uint8` и `int32`. При выводе через `%T` вы всегда увидите базовый тип.

**2. Комплексные числа редко нужны**
За 12+ лет разработки на C# они не понадобились ни разу. Типичный backend-разработчик работает с JSON, базами данных, HTTP — комплексные числа там не используются. Но хорошо знать, что они есть.

**3. Максимальные значения в примерах**
В коде показаны максимальные значения для каждого типа — это помогает понять диапазон:
- `int8`: 127
- `uint8`: 255
- `int16`: 32767
- `uint16`: 65535
- И так далее

**4. Одинарные кавычки для символов**
Rune-литералы записываются в одинарных кавычках:

```go
var r rune = 'A'   // OK
var s string = "A" // это строка, не rune!
```

**5. Форматирование float с точностью**
`.Nf` контролирует количество знаков после запятой:

```go
fmt.Printf("%.5f\n", 3.14159)   // 3.14159
fmt.Printf("%.15f\n", 2.71828)  // 2.718280000000000
```

**6. Универсальный %v**
Когда не знаете, какой шаблон использовать — используйте `%v`:

```go
fmt.Printf("%v\n", любое_значение)
```

## Что запомнить

- Переменные объявляются через `var имя тип = значение`
- Все типы работают одинаково — меняется только тип и диапазон
- Знаковые: `int8`, `int16`, `int32`, `int64`, `int`
- Беззнаковые: `uint8`, `uint16`, `uint32`, `uint64`, `uint`
- Вещественные: `float32`, `float64`
- Комплексные: `complex64`, `complex128` (редко используются)
- **`byte` = `uint8`** (для работы с сырыми данными)
- **`rune` = `int32`** (для Unicode символов)
- При выводе типа через `%T` альясы показываются как базовые типы
- `%v` — универсальный шаблон для любого типа
- `%c` — вывод символа из rune
- Одинарные кавычки `'A'` — для символов (rune)
- Двойные кавычки `"A"` — для строк

## Полезные ссылки

- [Go Specification — Types](https://go.dev/ref/spec#Types)
- [Go Specification — Numeric types](https://go.dev/ref/spec#Numeric_types)
- [Go Specification — String types](https://go.dev/ref/spec#String_types)
- [Go Blog — Strings, bytes, runes and characters](https://go.dev/blog/strings)
- [Package fmt — Format verbs](https://pkg.go.dev/fmt#hdr-Printing)
- [Effective Go](https://go.dev/doc/effective_go)
