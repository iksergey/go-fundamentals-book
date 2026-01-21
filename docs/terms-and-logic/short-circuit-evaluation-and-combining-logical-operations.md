---
title: Короткое вычисление
description: "Короткое вычисление short-circuit evaluation в Go: механизм оптимизации прекращает вычисление если результат известен, && первый false второй не вычисляется, || первый true второй не вычисляется, защита от деления на ноль паники индексации, порядок условий критичен, комбинирование логических операций && || !, практические примеры проверка доступа валидация форм права доступа, безопасность производительность кода."
keywords: "короткое вычисление golang, short-circuit evaluation, && прерывание false, || прерывание true, защита от паники, деление на ноль, индексация массива безопасность, порядок условий, комбинирование логических операций, оптимизация производительности go"
lang: ru
parent: Условия и логика
nav_order: 3
has_toc: false
---

# Короткое вычисление (Short-Circuit Evaluation) и комбинирование логических операций

### Определение

**Короткое вычисление** — механизм оптимизации, при котором компилятор Go **прекращает вычисление** логического выражения, как только результат становится известен. Это повышает производительность и обеспечивает безопасность кода.

### Как работает short-circuit для &&

**Правило:** если первый операнд — `false`, второй операнд **не вычисляется**.

**Логика:** для `&&` результат `true` только когда **оба** операнда `true`. Если первый — `false`, то независимо от второго результат всегда `false`.

```go
firstFalse := false
secondTrue := true

resultAnd := firstFalse && secondTrue
// Вычисление:
// 1. Проверяем firstFalse → false
// 2. Результат уже известен: false
// 3. secondTrue НЕ ВЫЧИСЛЯЕТСЯ!

fmt.Printf("false && true = %t\n", resultAnd)  // false
fmt.Println("Вторая часть (true) не вычислялась")
```

### Как работает short-circuit для ||

**Правило:** если первый операнд — `true`, второй операнд **не вычисляется**.

**Логика:** для `||` результат `false` только когда **оба** операнда `false`. Если первый — `true`, то независимо от второго результат всегда `true`.

```go
firstTrue := true
secondFalse := false

resultOr := firstTrue || secondFalse
// Вычисление:
// 1. Проверяем firstTrue → true
// 2. Результат уже известен: true
// 3. secondFalse НЕ ВЫЧИСЛЯЕТСЯ!

fmt.Printf("true || false = %t\n", resultOr)  // true
fmt.Println("Вторая часть (false) не вычислялась")
```

### Таблица: когда происходит короткое вычисление

| Оператор | Первый операнд | Результат известен | Второй вычисляется? |
|----------|----------------|--------------------|--------------------|
| `&&`     | `false`        | `false`            | ❌ НЕТ             |
| `&&`     | `true`         | Зависит от второго | ✅ ДА              |
| `\|\|`   | `true`         | `true`             | ❌ НЕТ             |
| `\|\|`   | `false`        | Зависит от второго | ✅ ДА              |

## Практическое применение: безопасность кода

### Пример 1: защита от деления на ноль

```go
value := 0

// ✅ БЕЗОПАСНО: второе условие не выполнится
canDivide := (value != 0)
fmt.Printf("value = %d\n", value)              // 0
fmt.Printf("value != 0: %t\n", canDivide)      // false
fmt.Println("Вторая часть не вычислялась")

// Если бы было:
// result := (value != 0) && (100 / value > 10)
// При value == 0:
// 1. (value != 0) → false
// 2. Короткое вычисление → второе условие НЕ выполняется
// 3. Деление на ноль НЕ произойдет!
```

### Пример 2: защита от выхода за границы массива

```go
// Слайс (динамический массив)
list := []int{10, 20, 30}
// Индексы:    0   1   2

// Попытка обращения к несуществующему индексу
fmt.Println("Элемент list:", list)  // 20 ✅

// ❌ ОШИБКА: panic: runtime error: index out of range
// fmt.Println("Элемент list[10]:", list[10])

// ✅ БЕЗОПАСНО: короткое вычисление спасает от паники
if list == 20 || list[10] == 12 {
    fmt.Println("Истина")
}
// Вычисление:
// 1. list == 20 → true
// 2. Результат известен: true
// 3. list[10] == 12 НЕ ВЫЧИСЛЯЕТСЯ → паники нет!
```

**Вывод:**
```
Элемент list: 20
Истина
```

### Пример 3: порядок условий имеет значение

```go
list := []int{10, 20, 30}

// ❌ ОПАСНО: сначала проверяем несуществующий индекс
// if list[10] == 12 || list == 20 {
//     // ПАНИКА: index out of range [10] with length 3
// }

// ✅ БЕЗОПАСНО: сначала проверяем существующий индекс
if list == 20 || list[10] == 12 {
    fmt.Println("Доступ безопасен")
}
```

### Пример 4: короткое вычисление с &&

```go
list := []int{10, 20, 30}

// ✅ БЕЗОПАСНО: первое условие ложно
if list != 20 && list[10] == 12 {
    fmt.Println("Это не выполнится")
}
// Вычисление:
// 1. list != 20 → false (т.к. list == 20)
// 2. Результат известен: false
// 3. list[10] == 12 НЕ ВЫЧИСЛЯЕТСЯ → паники нет!

fmt.Println("Программа продолжает работу")
```

### Пример 5: когда короткое вычисление НЕ срабатывает

```go
list := []int{10, 20, 30}

// ❌ ПАНИКА: первое условие true, второе БУДЕТ вычисляться
// if list == 20 && list[10] == 12 {
//     // runtime error: index out of range [10]
// }
// Вычисление:
// 1. list == 20 → true
// 2. Результат НЕ известен (нужно проверить второе)
// 3. list[10] == 12 ВЫЧИСЛЯЕТСЯ → ПАНИКА!
```

## Комбинирование логических условий

### Пример 1: проверка доступа (OR для альтернатив)

```go
isPublic := false
isOwner := true
isAdmin := false

// Доступ разрешён, если выполнено ХОТЯ БЫ ОДНО условие
hasAccess := isPublic || isOwner || isAdmin

fmt.Printf("hasAccess = %t || %t || %t = %t\n",
    isPublic, isOwner, isAdmin, hasAccess)
// Вывод: hasAccess = false || true || false = true
```

**Логика:** пользователь получает доступ, если:
- Ресурс публичный (`isPublic`)
- ИЛИ пользователь — владелец (`isOwner`) ✅
- ИЛИ пользователь — администратор (`isAdmin`)

### Пример 2: проверка валидности (AND для всех требований)

```go
hasName := true
hasEmail := false
hasPhone := true

// Все поля должны быть заполнены
allFieldsPresent := hasName && hasEmail && hasPhone
fmt.Printf("allFieldsPresent = %t\n", allFieldsPresent)  // false

// Инверсия: не все поля заполнены
notAllPresent := !allFieldsPresent
fmt.Printf("notAllPresent = %t\n", notAllPresent)  // true
```

**Логика:** форма валидна только если:
- Есть имя (`hasName`) ✅
- И есть email (`hasEmail`) ❌
- И есть телефон (`hasPhone`) ✅

Результат: `true && false && true` → `false`

### Пример 3: сложная бизнес-логика (чекаут в магазине)

```go
hasShippingAddress := true
hasPaymentMethod := true
totalAmount := 100.0
cartNotEmpty := (totalAmount > 0)

// Заказ можно оформить, если выполнены ВСЕ условия
canCheckout := hasShippingAddress && hasPaymentMethod && cartNotEmpty

fmt.Printf("hasShippingAddress = %t\n", hasShippingAddress)  // true
fmt.Printf("hasPaymentMethod = %t\n", hasPaymentMethod)      // true
fmt.Printf("cartNotEmpty = %t\n", cartNotEmpty)              // true
fmt.Printf("canCheckout = %t\n", canCheckout)                // true
```

**Требования для оформления заказа:**
1. ✅ Указан адрес доставки
2. ✅ Выбран способ оплаты
3. ✅ Корзина не пуста

Все условия выполнены → заказ можно оформить.

### Пример 4: комбинация NOT с AND

```go
isBlocked := false
isSuspended := false
accountActive := true

// Аккаунт доступен, если:
// - НЕ заблокирован
// - И НЕ приостановлен
// - И активен
canUseAccount := !isBlocked && !isSuspended && accountActive

fmt.Printf("canUseAccount = !%t && !%t && %t\n",
    isBlocked, isSuspended, accountActive)
// Вывод: canUseAccount = !false && !false && true
//        canUseAccount = true && true && true = true
```

**Логика доступа:**
- Аккаунт НЕ заблокирован (`!isBlocked`) → `!false` = `true` ✅
- И аккаунт НЕ приостановлен (`!isSuspended`) → `!false` = `true` ✅
- И аккаунт активен (`accountActive`) → `true` ✅

Результат: `true && true && true` → `true`

## Комбинирование с числовыми сравнениями

### Пример 5: возраст в диапазоне

```go
age := 25
minAge := 18
maxAge := 65

// Две проверки сравнения + логическое И
isInRange := (age >= minAge) && (age <= maxAge)

fmt.Printf("Возраст: %d\n", age)
fmt.Printf("Диапазон: [%d, %d]\n", minAge, maxAge)
fmt.Printf("В допустимом диапазоне? %t\n", isInRange)  // true
```

**Вычисление:**
1. `age >= minAge` → `25 >= 18` → `true`
2. `age <= maxAge` → `25 <= 65` → `true`
3. `true && true` → `true`

### Пример 6: валидация логина

```go
username := "admin"
password := "12345"

validUsername := (username == "admin")
validPassword := (password == "admin123")

// Доступ только если оба поля корректны
accessGranted := validUsername && validPassword

fmt.Printf("Username корректен? %t\n", validUsername)    // true
fmt.Printf("Password корректен? %t\n", validPassword)    // false
fmt.Printf("Доступ разрешён? %t\n", accessGranted)       // false
```

**Логика:**
- Логин правильный (`validUsername`) → `true` ✅
- Пароль НЕправильный (`validPassword`) → `false` ❌
- Результат: `true && false` → `false` ❌

### Пример 7: сравнение float с epsilon

```go
const epsilon = 1e-9

price1 := 10.01
price2 := 5.50
price3 := 4.51
total := price2 + price3  // 10.01 (с погрешностью)

// Комбинирование числового сравнения и логики
difference := math.Abs(total - price1)
pricesMatch := difference < epsilon

fmt.Printf("Цены совпадают? %t\n", pricesMatch)  // true
```

### Пример 8: площадь фигуры

```go
const epsilon = 1e-4  // 0.0001

width := 5.5
height := 3.2
area := width * height         // 17.6 (с погрешностью)
expectedArea := 17.6

// Комбинируем вычисления и проверку
difference := math.Abs(area - expectedArea)
areaCorrect := difference < epsilon

fmt.Printf("Площадь: %.2f\n", area)
fmt.Printf("Ожидаемая площадь: %.1f\n", expectedArea)
fmt.Printf("Площадь корректна? %t\n", areaCorrect)
```

## Практические сценарии

### Сценарий 1: проверка прав доступа к файлу

```go
isOwner := true
hasReadPermission := false
isAdmin := true

// Доступ к файлу, если:
// - Пользователь владелец
// - ИЛИ (есть права на чтение И не заблокирован)
// - ИЛИ администратор
canAccessFile := isOwner || (hasReadPermission && !isBlocked) || isAdmin

fmt.Printf("Доступ к файлу? %t\n", canAccessFile)  // true
```

### Сценарий 2: фильтрация пользователей

```go
age := 25
isVerified := true
isPremium := false
accountAge := 365  // дней

// Показывать контент, если:
// - Возраст >= 18
// - И (верифицирован ИЛИ премиум)
// - И аккаунт старше 30 дней
showContent := (age >= 18) && (isVerified || isPremium) && (accountAge > 30)

fmt.Printf("Показать контент? %t\n", showContent)  // true
```

### Сценарий 3: сортировка и упорядочивание

```go
a := 10
b := 20
c := 5

// Проверяем, что числа упорядочены по возрастанию
isAscending := (a < b) && (b < c)
fmt.Printf("Возрастающий порядок (a < b < c)? %t\n", isAscending)  // false

// Проверяем, что a между c и b
isBetween := (c < a) && (a < b)
fmt.Printf("a между c и b? %t\n", isBetween)  // true
```

## Важные правила короткого вычисления

### Правило 1: порядок условий критичен

```go
// ❌ ОПАСНО: может вызвать панику
// if (100 / value > 10) && (value != 0) { }

// ✅ БЕЗОПАСНО: сначала проверяем value != 0
if (value != 0) && (100 / value > 10) {
    // Деление выполнится только если value != 0
}
```

### Правило 2: используйте короткое вычисление для валидации

```go
input := ""

// ✅ Сначала проверяем, что строка не пустая
if len(input) > 0 && input[0] == '@' {
    fmt.Println("Начинается с @")
}

// ❌ Паника, если input пустая
// if input[0] == '@' && len(input) > 0 { }
```

### Правило 3: оптимизируйте дорогие операции

```go
// Дорогая операция (например, запрос к БД)
func expensiveCheck() bool {
    // Сложные вычисления
    return true
}

// ✅ Сначала дешёвая проверка
if isActive && expensiveCheck() {
    // expensiveCheck() вызовется только если isActive == true
}
```

## Ключевые моменты

1. **Короткое вычисление** — оптимизация компилятора
2. **Для `&&`:** если первый `false` → второй не вычисляется
3. **Для `||`:** если первый `true` → второй не вычисляется
4. **Защита от паник:** порядок условий важен для безопасности
5. **Производительность:** дорогие операции размещайте справа
6. **Комбинирование:** можно миксовать `&&`, `||`, `!`, сравнения
7. **Скобки:** явно управляют приоритетом и читаемостью
8. **Индексация массивов:** начинается с `0`
9. **Валидация форм:** используйте `&&` для всех обязательных полей
10. **Права доступа:** используйте `||` для альтернативных условий

## Best Practices

### 1. Размещайте дешёвые проверки слева

```go
// ✅ Сначала проверка переменной (быстро)
if isEnabled && complexCalculation() {
    // complexCalculation() может не выполниться
}

// ❌ Дорогая операция выполнится всегда
// if complexCalculation() && isEnabled { }
```

### 2. Защищайте от nil/null

```go
var ptr *int

// ✅ Сначала проверяем на nil
if ptr != nil && *ptr > 0 {
    fmt.Println("Значение:", *ptr)
}

// ❌ Паника при ptr == nil
// if *ptr > 0 && ptr != nil { }
```

### 3. Валидируйте длину перед обращением

```go
arr := []int{1, 2, 3}
index := 5

// ✅ Проверяем границы
if index < len(arr) && arr[index] > 0 {
    fmt.Println("Элемент:", arr[index])
}
```

### 4. Комбинируйте логику читаемо

```go
// ✅ Разделяйте сложные условия
hasAccess := isPublic || isOwner || isAdmin
hasPermissions := hasReadAccess && hasWriteAccess
canModify := hasAccess && hasPermissions

// ❌ Всё в одну строку
// canModify := (isPublic || isOwner || isAdmin) && (hasReadAccess && hasWriteAccess)
```

### 5. Документируйте сложные условия

```go
// Пользователь может редактировать, если:
// 1. Он владелец ИЛИ админ
// 2. И документ не заблокирован
// 3. И есть права на редактирование
canEdit := (isOwner || isAdmin) && !isLocked && hasEditPermission
```

## Что запомнить

- **Short-circuit** экономит время выполнения
- **`&&`** прерывается на `false`
- **`||`** прерывается на `true`
- **Порядок условий** влияет на безопасность
- **Дешёвые проверки** размещайте слева
- **Комбинирование** позволяет создавать сложную логику
- **Индексы массивов** начинаются с `0`
- **Скобки** повышают читаемость
- **Epsilon** используйте для float-сравнений
- **Валидация** строится на комбинировании операторов
