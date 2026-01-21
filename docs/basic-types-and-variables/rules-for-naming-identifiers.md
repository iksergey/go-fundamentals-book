---
title: Правила именования идентификаторов
description: "Правила создания имён в Go: разрешённые символы, регистрозависимость, 25 ключевых слов, camelCase соглашения, осмысленные имена vs короткие, экспорт через регистр первой буквы."
keywords: "идентификаторы go, правила именования golang, ключевые слова go, camelCase go, регистрозависимость golang, naming identifiers go, reserved keywords"
lang: ru
parent: Базовые типы и переменные
nav_order: 9
has_toc: false
---

# Правила именования идентификаторов

## Краткое описание

В этом уроке изучаем правила именования переменных, функций и других сущностей в Go. Разбираем, какие символы разрешены в идентификаторах, почему нельзя использовать ключевые слова, и учимся давать осмысленные имена переменным согласно best practices языка.

## Ключевые концепции

### Что такое идентификатор

**Идентификатор** — это имя, которое программист даёт переменной, функции, типу или другой сущности в программе. В Go идентификаторы должны соответствовать строгим правилам.

### Регистрозависимость

Go **чувствителен к регистру** — переменные с разным регистром букв считаются разными:

```go
var value int = 10
var Value int = 20
var VALUE int = 30

// Это три разные переменные!
fmt.Printf("value=%d, Value=%d, VALUE=%d\n", value, Value, VALUE)
```

Более того, регистр первой буквы определяет **видимость**:
- Заглавная буква → экспортируемая (public)
- Строчная буква → неэкспортируемая (private)

## Правила именования идентификаторов

### Разрешено

**1. Начинать с буквы Unicode (верхний или нижний регистр):**

```go
var age int = 25
var Age int = 30
var firstName string = "Иван"
```

**2. Начинать с символа подчёркивания `_`:**

```go
var _private int = 100
var _temp string = "temporary"
```

Обычно используется для приватных или временных переменных.

**3. Содержать цифры (но не на первой позиции):**

```go
var userName123 string = "user"
var value2 int = 42
var param1 string = "test"
```

**4. Unicode символы (кириллица, иероглифы):**

```go
var температура int = 20       // кириллица
var 年齢 int = 30               // китайские иероглифы
var имя string = "Сергей"
```

**Важно:** хотя технически это разрешено, использовать не латинские символы **не рекомендуется** в реальных проектах. Это не стандарт индустрии, и коллеги могут не оценить.

### Запрещено

**1. Начинать с цифры:**

```go
// var 123name string = "error"  // ОШИБКА!
// var 1stPlace int = 1          // ОШИБКА!
```

**2. Использовать дефис `-`:**

```go
// var my-name string = "error"  // ОШИБКА!
// var user-id int = 100         // ОШИБКА!
```

Вместо дефиса используйте camelCase: `myName`, `userId`.

**3. Использовать эмодзи и специальные символы:**

```go
// var 😳 string = "emoji"       // ОШИБКА!
// var user@name string = "error" // ОШИБКА!
```

**4. Использовать ключевые слова Go:**

```go
// var for int = 10              // ОШИБКА! 'for' зарезервировано
// var func string = "error"     // ОШИБКА! 'func' зарезервировано
// var if bool = true            // ОШИБКА! 'if' зарезервировано
```

## Ключевые слова Go

В Go **25 зарезервированных ключевых слов**, которые нельзя использовать как идентификаторы:

```
break        default      func         interface    select
case         defer        go           map          struct
chan         else         goto         package      switch
const        fallthrough  if           range        type
continue     for          import       return       var
```

**Где найти список:** Go Specification → Keywords

**Обход ограничения:** если нужно использовать похожее имя, можно изменить регистр:

```go
// var else int = 10  // ОШИБКА!
var Else int = 10     // OK (но не рекомендуется)
```

Хотя технически это работает, давать переменным имена ключевых слов с заглавной буквы — плохая практика.

## Best practices именования

### Используйте осмысленные имена

**Плохо:**

```go
var fn string = "Иван"
var a int = 25
var x float64 = 99.99
```

**Хорошо:**

```go
var firstName string = "Иван"
var age int = 25
var price float64 = 99.99
```

### camelCase для составных имён

В Go принято использовать camelCase без разделителей:

```go
var userName string = "admin"
var totalStudentsCount int = 150
var averageTemperatureInCelsius float64 = 22.5
```

### Длинные имена — это нормально

Не бойтесь длинных, но понятных имён:

```go
var maxConnectionRetries int = 3
var httpRequestTimeout time.Duration = 30 * time.Second
var databaseConnectionString string = "postgres://..."
```

Лучше длинное и понятное имя, чем короткое и загадочное.

### Короткие имена для локальных переменных

В небольших областях видимости допустимы короткие имена:

```go
for i := 0; i < 10; i++ {
    // i — понятно из контекста
}

if err := doSomething(); err != nil {
    // err — стандартное соглашение
}
```

### Контекст важнее длины

Если контекст понятен, можно использовать короткие имена:

```go
// В функции работы с пользователями
func GetUser(id int) User {
    // id понятно из контекста
}

// В математической функции
func CalculateDistance(x1, y1, x2, y2 float64) float64 {
    // координаты понятны
}
```

## Практика

### Полный пример из урока

```go
package main

import "fmt"

func main() {
    // Правильные идентификаторы
    var age int = 25
    var firstName string = "Сергей"
    var _private int = 100
    var userName123 string = "user"
    var температура int = 20  // Unicode (не рекомендуется)
    var 年齢 int = 30          // Иероглифы (не рекомендуется)

    fmt.Println(age, firstName, _private, userName123, температура, 年齢)

    // Регистрозависимость
    var value int = 10
    var Value int = 20
    var VALUE int = 30
    fmt.Printf("value=%d, Value=%d, VALUE=%d\n", value, Value, VALUE)

    // Длинные описательные имена (рекомендуется)
    var totalStudentsCount int = 150
    var averageTemperatureInCelsius float64 = 22.5
    fmt.Println(totalStudentsCount, averageTemperatureInCelsius)

    // НЕПРАВИЛЬНЫЕ идентификаторы (не компилируются):
    // var 123name string = "error"       // цифра в начале
    // var my-name string = "error"       // дефис
    // var 😳 string = "emoji"            // эмодзи
    // var for int = 10                   // ключевое слово
    // var func string = "error"          // ключевое слово
}
```

### Примеры хороших имён

```go
// Переменные
var userAge int = 25
var isActive bool = true
var maxRetryCount int = 3

// Константы (PascalCase или UPPER_CASE)
const MaxConnections = 100
const DefaultTimeout = 30

// Функции
func CalculateTotal() int { }
func GetUserByID(id int) User { }

// Типы
type UserAccount struct { }
type RequestHandler interface { }
```

## Важные моменты

**1. Используйте латиницу**
Хотя Unicode поддерживается, в профессиональной разработке используйте только латинские буквы. Это стандарт индустрии и требование большинства code review процессов.

**2. Не злоупотребляйте подчёркиванием**
Переменные с `_` в начале имеют специфичное значение (обычно private/internal). Используйте их осознанно.

**3. Проверяйте ключевые слова**
Перед использованием незнакомого имени убедитесь, что это не ключевое слово. Список всегда доступен в Go Specification.

**4. Контекст определяет длину**
- Глобальные переменные → длинные описательные имена
- Локальные переменные в малом scope → можно короче
- Цикловые счётчики → `i`, `j`, `k`

**5. Соглашения команды важнее**
Даже если правила Go что-то разрешают, в проекте могут быть свои стандарты. Всегда изучайте code style проекта при входе в команду.

**6. Go fmt не исправит имена**
`gofmt` форматирует код, но не меняет имена переменных. Ответственность за осмысленные имена лежит на программисте.

**7. Экспорт через регистр**
```go
var Public int = 10   // доступна вне пакета
var private int = 20  // только внутри пакета
```

Это уникальная особенность Go — видимость определяется регистром, а не ключевыми словами.

## Что запомнить

- Идентификатор — имя переменной, функции, типа
- Должен начинаться с буквы (Unicode) или `_`
- Может содержать цифры (но не в начале)
- **Нельзя:** начинать с цифры, использовать дефисы, эмодзи, ключевые слова
- Go регистрозависимый: `value` ≠ `Value` ≠ `VALUE`
- 25 ключевых слов нельзя использовать (`for`, `func`, `if`, `var` и др.)
- Unicode разрешён, но **не рекомендуется** в реальных проектах
- Используйте **camelCase** для составных имён
- Длинные осмысленные имена лучше коротких загадочных
- Заглавная буква → public, строчная → private
- Следуйте code style проекта/команды
- Список ключевых слов: Go Specification → Keywords

## Полезные ссылки

- [Go Specification — Identifiers](https://go.dev/ref/spec#Identifiers)
- [Go Specification — Keywords](https://go.dev/ref/spec#Keywords)
- [Effective Go — Names](https://go.dev/doc/effective_go#names)
- [Go Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments)
- [Uber Go Style Guide](https://github.com/uber-go/guide/blob/master/style.md)
