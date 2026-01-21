---
title: Все типы данных в одном примере
description: "Практическая демонстрация всех базовых типов Go: знаковые int8-int64, беззнаковые uint8-uint64, float32/64, complex числа, альясы byte/rune, переполнение типов и точность float."
keywords: "типы данных go примеры, переполнение uint8 golang, float32 точность, rune unicode code point, byte uint8 альяс, комплексные числа go"
lang: ru
parent: Базовые типы и переменные
nav_order: 6
has_toc: false
---

# Все типы данных в одном примере

## Краткое описание

Практический урок с демонстрацией всех базовых типов данных Go в одной программе. Разбираем знаковые и беззнаковые целые числа, float, комплексные числа, bool, string и специальные типы-альясы byte и rune.

## Основные концепции

### Универсальность синтаксиса

Все типы данных объявляются одинаково через `var`:

```go
var intVar int = 100
var floatVar float32 = 3.14
var boolVar bool = true
var stringVar string = "Hello"
```

Меняется только тип и диапазон значений — синтаксис остаётся единым.

### Альясы типов

**byte и rune — это альясы существующих типов:**

```go
var byteVar byte = 255     // на самом деле uint8
var runeVar rune = 'Я'     // на самом деле int32

fmt.Printf("%T\n", byteVar)  // выведет: uint8
fmt.Printf("%T\n", runeVar)  // выведет: int32
```

Шаблон `%T` показывает базовый тип, а не альяс.

### Комплексные числа

Go поддерживает `complex64` и `complex128` "из коробки":

```go
var c complex64 = 1 + 2i
fmt.Printf("%v\n", c)  // (1+2i)
```

**Важно:** за 12+ лет backend-разработки на C# они не понадобились ни разу. В типичных задачах (REST API, JSON, базы данных) не используются.

## Практический пример

Программа демонстрирует все типы с выводом значений и типов:

```go
// Знаковые целые
var int8Var int8 = 127
fmt.Printf("int8: %d (тип: %T)\n", int8Var, int8Var)

// Беззнаковые целые
var uint8Var uint8 = 255
fmt.Printf("uint8: %d (тип: %T)\n", uint8Var, uint8Var)

// Float
var float32Var float32 = 3.14159
fmt.Printf("float32: %.5f (тип: %T)\n", float32Var, float32Var)

// Альясы
var byteVar byte = 255      // альяс uint8
var runeVar rune = 'Я'      // альяс int32
fmt.Printf("byte: %d (тип: %T)\n", byteVar, byteVar)
// Вывод: byte: 255 (тип: uint8)
```

## Что запомнить

- Синтаксис объявления одинаков для всех типов: `var имя тип = значение`
- Комплексные числа существуют, но в backend почти не нужны
- `byte` — это `uint8` (для работы с байтами)
- `rune` — это `int32` (для Unicode символов)
- `%T` показывает базовый тип альяса
- Все типы работают аналогично — привыкайте к синтаксису Go
- Для практики достаточно знать `int`, `float64`, `bool`, `string`

## Полезные ссылки

- [Go Specification — Types](https://go.dev/ref/spec#Types)
- [Go Specification — Numeric types](https://go.dev/ref/spec#Numeric_types)
- [Go Blog — Strings, bytes, runes](https://go.dev/blog/strings)
- [Package fmt](https://pkg.go.dev/fmt)
