---
title: Экспорт идентификаторов и соглашения об аббревиатурах
description: "Система видимости в Go через регистр: заглавная буква для экспорта, строчная для приватности. Правила аббревиатур ID/HTTP/URL, camelCase для локальных переменных, префиксы is/has для bool."
keywords: "экспорт идентификаторов go, public private golang, naming conventions go, аббревиатуры golang, camelCase go, exported identifiers, видимость пакетов go"
lang: ru
parent: Базовые типы и переменные
nav_order: 10
has_toc: false
---

# Экспорт идентификаторов и соглашения об аббревиатурах

## Краткое описание

В этом уроке изучаем систему видимости идентификаторов в Go через регистр первой буквы: заглавная делает сущность публичной (экспортируемой), строчная — приватной. Разбираем, как это работает с переменными, функциями и структурами, и учимся правильно писать аббревиатуры согласно Go conventions.

## Ключевые концепции

### Экспорт через регистр

В Go **нет ключевых слов** `public`/`private`. Видимость определяется регистром первой буквы идентификатора:

**Заглавная буква → экспортируемый (публичный):**
```go
var PublicVariable int = 100      // доступна вне пакета
func ExportedFunction() { }        // доступна вне пакета
type ExportedStruct struct { }     // доступна вне пакета
```

**Строчная буква → неэкспортируемый (приватный):**
```go
var privateVariable int = 200      // только внутри пакета
func internalFunction() { }        // только внутри пакета
type internalStruct struct { }     // только внутри пакета
```

### Область применения

Правило регистра работает для всех сущностей:
- **Переменные**: `PublicVar` vs `privateVar`
- **Функции**: `ExportedFunc()` vs `internalFunc()`
- **Структуры**: `PublicStruct` vs `privateStruct`
- **Поля структур**: `User.Name` (public) vs `User.password` (private)
- **Методы**: `obj.PublicMethod()` vs `obj.privateMethod()`
- **Константы**: `MaxValue` vs `maxValue`
- **Интерфейсы**: `Reader` vs `reader`

## Работа с пакетами

### Создание пакета с экспортируемыми сущностями

**Файл: `testmodule/models.go`**

```go
package testmodule

// Экспортируемая переменная (доступна вне пакета)
var PublicVariable int = 100

// Неэкспортируемая переменная (только внутри testmodule)
var privateVariable int = 200
```

### Использование в другом пакете

**Файл: `main.go`**

```go
package main

import (
    "ex06namingconventions/testmodule"
    "fmt"
)

func main() {
    // OK - PublicVariable экспортируется
    fmt.Println(testmodule.PublicVariable)  // 100
    
    // ОШИБКА - privateVariable не экспортируется
    // fmt.Println(testmodule.privateVariable)
    // cannot refer to unexported name testmodule.privateVariable
}
```

Go автоматически подчёркивает попытки обращения к неэкспортируемым идентификаторам в IDE.

### Структуры с разными уровнями видимости

```go
package models

// Экспортируемая структура
type User struct {
    Name     string  // экспортируемое поле
    Age      int     // экспортируемое поле
    password string  // приватное поле
}

// Приватная структура
type session struct {
    token string
}
```

**Использование:**

```go
package main

import "myapp/models"

func main() {
    user := models.User{
        Name: "Иван",     // OK
        Age: 25,          // OK
        // password: "123" // ОШИБКА - поле приватное
    }
    
    // var s models.session  // ОШИБКА - тип приватный
}
```

## Соглашения об аббревиатурах

### Правило: все заглавные или все строчные

В Go аббревиатуры пишутся **либо полностью заглавными, либо полностью строчными** — но не смешанно:

**Правильно:**

```go
var userID int = 123              // ID (не userId)
var HTTPServer string = "nginx"   // HTTP (не HttpServer)
var urlPath string = "/api"       // url (не uRLPath)
var xmlData string = "<root/>"    // xml (не xMLData)
var apiKey string = "secret"      // api (не aPIKey)
```

**Неправильно (но часто встречается в других языках):**

```go
// var userId int = 123           // ❌ C#/Java стиль
// var HttpServer string = "..."  // ❌ смешанный регистр
// var uRLPath string = "..."     // ❌ частично заглавные
```

### Распространённые аббревиатуры

| Аббревиатура | Правильно (экспорт) | Правильно (приват) | Неправильно |
|--------------|---------------------|---------------------|-------------|
| ID | `UserID` | `userID` | `UserId`, `userId` |
| HTTP | `HTTPServer` | `httpServer` | `HttpServer` |
| URL | `URLPath` | `urlPath` | `UrlPath` |
| XML | `XMLData` | `xmlData` | `XmlData` |
| API | `APIKey` | `apiKey` | `ApiKey` |
| JSON | `JSONResponse` | `jsonResponse` | `JsonResponse` |
| HTML | `HTMLTemplate` | `htmlTemplate` | `HtmlTemplate` |
| SQL | `SQLQuery` | `sqlQuery` | `SqlQuery` |
| DB | `DBConnection` | `dbConnection` | `DbConnection` |

## Best Practices именования

### 1. camelCase для локальных переменных

```go
var userName string = "Сергей"
var userAge int = 25
var totalStudentsCount int = 150
```

### 2. Префикс `is`/`has` для булевых переменных

```go
var isActive bool = true
var isSuccess bool = true
var hasAccess bool = false
var canEdit bool = true
```

Это делает код читаемым как естественный язык:

```go
if isActive && hasAccess {
    // разрешить действие
}
```

### 3. Описательные имена для больших scope

Чем шире область видимости, тем подробнее должно быть имя:

```go
// Глобальная/экспортируемая
var MaxConnectionTimeout int = 3000
var DefaultDatabaseConnectionString string = "..."

// Локальная в функции
var timeout int = 30
var conn *sql.DB
```

### 4. Избегайте бессмысленных имён

**Плохо:**

```go
var foo int
var bar string
var temp float64
var x User
```

**Хорошо:**

```go
var studentScore int
var errorMessage string
var averagePrice float64
var currentUser User
```

### 5. Контекст определяет длину

**В глобальной области:**

```go
var CurrentUserAuthenticationToken string = "abc123"
```

**В узком контексте:**

```go
func ProcessUser(id int) {
    // id понятно из контекста функции
}

for i := 0; i < len(items); i++ {
    // i стандартно для счётчика
}
```

## Практика

### Полный пример из урока

**testmodule/models.go:**

```go
package testmodule

// Экспортируемые (публичные) идентификаторы
var PublicVariable int = 100

// Неэкспортируемые (приватные) идентификаторы
var privateVariable int = 200
```

**main.go:**

```go
package main

import (
    "ex06namingconventions/testmodule"
    "fmt"
)

func main() {
    // Локальные переменные (camelCase)
    var userName string = "Сергей"
    var userAge int = 25
    var totalStudentsCount int = 150
    var isActive bool = true
    var maxConnectionTimeout int = 3000

    fmt.Printf("userName: %s\n", userName)
    fmt.Printf("userAge: %d\n", userAge)
    fmt.Printf("totalStudentsCount: %d\n", totalStudentsCount)
    fmt.Printf("isActive: %t\n", isActive)
    fmt.Printf("maxConnectionTimeout: %d\n", maxConnectionTimeout)

    // Использование экспортируемых идентификаторов
    fmt.Printf("PublicVariable: %d\n", testmodule.PublicVariable)
    // fmt.Printf("privateVariable: %d\n", testmodule.privateVariable) // ОШИБКА!

    // Описательные имена
    var currentUserAuthenticationToken string = "abc123xyz"
    fmt.Println(currentUserAuthenticationToken)

    // Осмысленные имена
    var studentScore int = 95
    var errorMessage string = "Failed"
    fmt.Println(studentScore, errorMessage)

    // Правильное написание аббревиатур
    var userID int = 123            // не userId
    var HTTPServer string = "nginx" // не HttpServer
    var urlPath string = "/api"     // не uRLPath
    fmt.Println(userID, HTTPServer, urlPath)
}
```

### Примеры структур

```go
package models

// Публичная структура с разными полями
type User struct {
    ID       int     // экспортируемое
    Name     string  // экспортируемое
    Email    string  // экспортируемое
    password string  // приватное
}

// Публичная функция
func (u *User) GetID() int {
    return u.ID
}

// Приватная функция
func (u *User) validatePassword() bool {
    return len(u.password) > 8
}

// Приватная структура
type session struct {
    userID int
    token  string
}
```

## Важные моменты

**1. IDE подсвечивает ошибки экспорта**
Современные IDE (VS Code, GoLand) автоматически показывают, когда вы пытаетесь обратиться к неэкспортируемому идентификатору.

**2. Экспорт — на уровне пакета**
Внутри одного пакета все идентификаторы видны, независимо от регистра. Экспорт работает только между пакетами.

**3. Поля структур тоже экспортируются**
```go
type User struct {
    Name string  // видно вне пакета
    age  int     // только внутри пакета
}
```

**4. Методы подчиняются тем же правилам**
```go
func (u *User) Export() { }    // публичный метод
func (u *User) internal() { }  // приватный метод
```

**5. Аббревиатуры — это convention, не правило**
Компилятор не заставит писать `HTTPServer` вместо `HttpServer`, но Go community ожидает первого варианта.

**6. Go vet и linters проверяют**
Инструменты вроде `golint`, `staticcheck` предупредят о нарушениях naming conventions.

**7. Приватные поля требуют геттеров/сеттеров**
```go
type User struct {
    name string  // приватное
}

// Публичный геттер
func (u *User) Name() string {
    return u.name
}

// Публичный сеттер
func (u *User) SetName(name string) {
    u.name = name
}
```

**8. Константы подчиняются тем же правилам**
```go
const MaxConnections = 100     // экспортируемая
const defaultTimeout = 30      // приватная
```

## Что запомнить

- **Заглавная буква = public**, **строчная = private**
- Нет ключевых слов `public`/`private` — всё через регистр
- Работает для всех сущностей: переменных, функций, структур, полей, методов
- Внутри пакета всё видно, экспорт работает между пакетами
- Аббревиатуры: **все заглавные или все строчные**
  - `UserID`, `HTTPServer`, `URLPath` (экспорт)
  - `userID`, `httpServer`, `urlPath` (приват)
  - Не `userId`, `HttpServer`, `UrlPath`
- camelCase для локальных переменных
- Префикс `is`/`has` для булевых значений
- Длинные имена для широкого scope, короткие для узкого
- Осмысленные имена всегда лучше коротких
- IDE подсвечивает ошибки доступа к приватным идентификаторам
- Linters (golint, staticcheck) проверяют naming conventions

## Полезные ссылки

- [Go Specification — Exported identifiers](https://go.dev/ref/spec#Exported_identifiers)
- [Effective Go — Names](https://go.dev/doc/effective_go#names)
- [Go Code Review Comments — Naming](https://github.com/golang/go/wiki/CodeReviewComments#naming)
- [Uber Go Style Guide — Naming](https://github.com/uber-go/guide/blob/master/style.md#naming)
- [Go Wiki — Common naming patterns](https://github.com/golang/go/wiki/CodeReviewComments)
