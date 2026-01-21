---
title: Логические операторы
description: "Логические операторы в Go: && (И) оба true, || (ИЛИ) хотя бы один true, ! (НЕ) инверсия, XOR через != неравенство, таблицы истинности, приоритет ! > && > ||, короткое вычисление short-circuit evaluation, скобки для управления порядком, практические примеры диапазоны HTTP-статусы выходные дни."
keywords: "логические операторы golang, && И конъюнкция, || ИЛИ дизъюнкция, ! НЕ инверсия, XOR != неравенство, таблицы истинности, приоритет операторов, short-circuit evaluation, проверка диапазона, скобки условия go"
lang: ru
parent: Условия и логика
nav_order: 2
has_toc: false
---

# Логические операторы

## Краткое описание

Логические операторы — фундаментальные инструменты для построения сложных условий в программировании. В Go доступны три основные логическиеские операции: **И** (`&&`), **ИЛИ** (`||`) и **НЕ** (`!`), которые работают с булевыми значениями и возвращают `bool` . Эти операторы используются для комбинирования простых условий в сложные выражения, управления потоком программы и валидации данных .

## Логическое И: && (AND, конъюнкция)

### Определение

**Логическое И** — бинарная операция, возвращающая `true` только когда **оба операнда** равны `true`. Во всех остальных случаях результат — `false`.

```go
a := true
b := true
c := false

fmt.Printf("true && true:   %t\n", a && b)  // true
fmt.Printf("true && false:  %t\n", a && c)  // false
fmt.Printf("false && true:  %t\n", c && a)  // false
fmt.Printf("false && false: %t\n", c && c)  // false
```

### Таблица истинности для &&

| A     | B     | A && B |
|-------|-------|--------|
| true  | true  | true   |
| true  | false | false  |
| false | true  | false  |
| false | false | false  |

### Практическое применение: проверка диапазона

**Задача:** проверить, находится ли число B между числами A и C.

**Математическое условие:** A < B < C

```go
// Геометрическое представление:
// ----A--------B--------C----→
//     |        |        |
//   minAge    age    maxAge

age := 25
minAge := 18
maxAge := 65

// Проверяем три условия одновременно:
// 1. age >= minAge (возраст больше минимума)
// 2. age <= maxAge (возраст меньше максимума)
isInRange := (age >= minAge) && (age <= maxAge)

fmt.Printf("Возраст: %d\n", age)
fmt.Printf("Диапазон: [%d, %d]\n", minAge, maxAge)
fmt.Printf("В допустимом диапазоне? %t\n", isInRange) // true
```

### Почему нет тройных неравенств в Go?

Попытка записать условие как `minAge <= age <= maxAge` **не работает** в Go:

```go
// ❌ ОШИБКА КОМПИЛЯЦИИ
// resH := minAge <= h <= maxAge

// ✅ ПРАВИЛЬНО: разбиваем на два сравнения
isInRange := (age >= minAge) && (age <= maxAge)
```

**Причина:** Go сначала вычисляет `minAge <= h` → `bool`, затем пытается сравнить `bool` с `maxAge` (целым числом) — типы несовместимы.

### Числовой пример

```go
x := 10
y := 20
z := 5

// Проверяем, что x находится между z и y
inRange := (x > z) && (x < y)  // (10 > 5) && (10 < 20)
fmt.Printf("(x > z) && (x < y): %t\n", inRange)  // true
```

## Логическое ИЛИ: || (OR, дизъюнкция)

### Определение

**Логическое ИЛИ** — бинарная операция, возвращающая `true`, если **хотя бы один** операнд равен `true`. Возвращает `false` только когда **оба** операнда — `false`.

```go
d := true
e := false

fmt.Printf("true || true:   %t\n", d || d)  // true
fmt.Printf("true || false:  %t\n", d || e)  // true
fmt.Printf("false || true:  %t\n", e || d)  // true
fmt.Printf("false || false: %t\n", e || e)  // false
```

### Таблица истинности для ||

| A     | B     | A \|\| B |
|-------|-------|----------|
| true  | true  | true     |
| true  | false | true     |
| false | true  | true     |
| false | false | false    |

### Практическое применение: проверка HTTP-статусов

```go
status1 := 200
status2 := 404

// Проверяем успешность запроса (200 или 201)
isSuccess := (status1 == 200) || (status1 == 201)
fmt.Printf("Успешный запрос? %t\n", isSuccess)  // true

// Проверяем ошибку "не найдено"
isNotFound := (status2 == 404) || (status2 == 500)
fmt.Printf("Ресурс не найден? %t\n", isNotFound)  // true
```

### Пример с строками: выходные дни

```go
day := "Saturday"

// Определяем, является ли день субботой
isSaturday := (day == "Saturday")

// Определяем, является ли день воскресеньем
isSunday := (day == "Sunday")

// День — выходной, если это суббота ИЛИ воскресенье
isWeekend := isSaturday || isSunday

fmt.Printf("День: %s\n", day)
fmt.Printf("Выходной? %t\n", isWeekend)  // true
```

**Важно:** сравнение строк в Go чувствительно к регистру.

## Логическое НЕ: ! (NOT, инверсия/отрицание)

### Определение

**Логическое НЕ** — унарная операция, инвертирующая значение:
- `!true` → `false`
- `!false` → `true`

```go
g := true
h := false

notG := !g  // !true = false
notH := !h  // !false = true

fmt.Printf("!true:  %t\n", notG)  // false
fmt.Printf("!false: %t\n", notH)  // true
```

### Таблица истинности для !

| A     | !A    |
|-------|-------|
| true  | false |
| false | true  |

### Практическое применение

```go
isLoggedIn := true
isAdmin := false

// Проверяем, что пользователь НЕ авторизован
notLoggedIn := !isLoggedIn
fmt.Printf("Пользователь не авторизован? %t\n", notLoggedIn)  // false

// Проверяем, что пользователь НЕ администратор
notAdmin := !isAdmin
fmt.Printf("Не администратор? %t\n", notAdmin)  // true
```

### Реальные сценарии

```go
isEmpty := false
notEmpty := !isEmpty
fmt.Printf("Список не пуст? %t\n", notEmpty)  // true

isValidEmail := true
isInvalidEmail := !isValidEmail
fmt.Printf("Email невалиден? %t\n", isInvalidEmail)  // false
```

## XOR (исключающее ИЛИ) в Go

### Почему нет явного XOR для bool?

В Go **отсутствует** оператор XOR для логических типов, в отличие от Java, C#, Python. Сообщество Go отклонило его добавление из-за философии упрощения языка.

### Определение XOR

**XOR** (разделительная дизъюнкция) — операция, возвращающая `true`, когда **строго один** из операндов равен `true`.

### Как реализовать XOR в Go?

Используйте оператор **неравенства** `!=`:

```go
a := true
b := false

// XOR через неравенство
result := a != b  // true != false → true

fmt.Printf("XOR %t != %t = %t\n", a, b, result)  // true
```

### Полная таблица истинности для XOR

```go
a := true
b := true
result := a != b  // true != true → false
fmt.Printf("XOR %t != %t = %t\n", a, b, result)  // false

a = true
b = false
result = a != b  // true != false → true
fmt.Printf("XOR %t != %t = %t\n", a, b, result)  // true

a = false
b = true
result = a != b  // false != true → true
fmt.Printf("XOR %t != %t = %t\n", a, b, result)  // true

a = false
b = false
result = a != b  // false != false → false
fmt.Printf("XOR %t != %t = %t\n", a, b, result)  // false
```

| A     | B     | A XOR B (A != B) |
|-------|-------|------------------|
| true  | true  | false            |
| true  | false | true             |
| false | true  | true             |
| false | false | false            |

## Приоритет логических операторов

### Иерархия (от высшего к низшему)

1. **`!`** (НЕ) — самый высокий приоритет
2. **`&&`** (И) — средний приоритет
3. **`||`** (ИЛИ) — самый низкий приоритет

### Пример 1: приоритет && выше ||

```go
// Выражение: true || false && false
// Порядок: true || (false && false)

// Шаг 1: вычисляем && (выше приоритет)
step1 := false && false  // false

// Шаг 2: вычисляем ||
step2 := true || step1   // true || false → true

fmt.Println("true || false && false =", step2)  // true
```

### Пример 2: приоритет ! выше ||

```go
// Выражение: !true || false
// Порядок: (!true) || false

// Шаг 1: вычисляем ! (самый высокий приоритет)
step1 := !true  // false

// Шаг 2: вычисляем ||
step2 := step1 || false  // false || false → false

fmt.Println("!true || false =", step2)  // false
```

### Изменение приоритета скобками

Скобки `()` позволяют явно управлять порядком вычислений:

```go
// БЕЗ скобок: && выполняется первым
withoutParens := true || false && false
// true || (false && false) → true || false → true
fmt.Printf("true || false && false = %t\n", withoutParens)  // true

// СО скобками: || выполняется первым
withParens := (true || false) && false
// (true || false) && false → true && false → false
fmt.Printf("(true || false) && false = %t\n", withParens)  // false
```

### Практический совет

**Всегда используйте скобки** для сложных выражений, даже если приоритет очевиден — это повышает читаемость:

```go
// Читаемо и понятно
isValid := (age >= 18) && (age <= 65) && (hasLicense == true)

// Менее понятно
isValid := age >= 18 && age <= 65 && hasLicense == true
```

## Короткое вычисление (short-circuit evaluation)

### Принцип

Go **не вычисляет** второй операнд, если результат определён первым:

**Для `&&`:** если первый операнд — `false`, второй не вычисляется (результат уже `false`)

**Для `||`:** если первый операнд — `true`, второй не вычисляется (результат уже `true`)

### Пример с &&

```go
firstFalse := false
secondTrue := true

result := firstFalse && secondTrue
// Вычисление: firstFalse == false → результат false
// secondTrue НЕ вычисляется!

fmt.Println("false && true =", result)  // false
```

### Пример с ||

```go
firstTrue := true
secondFalse := false

result := firstTrue || secondFalse
// Вычисление: firstTrue == true → результат true
// secondFalse НЕ вычисляется!

fmt.Println("true || false =", result)  // true
```

### Практическое значение

Короткое вычисление позволяет безопасно проверять условия:

```go
value := 0

// Безопасно: проверяем value != 0 ПЕРЕД делением
canDivide := (value != 0)
fmt.Printf("Можно делить? %t\n", canDivide)

// Если бы был второй операнд с делением:
// result := (value != 0) && (100 / value > 10)
// При value == 0 деление НЕ выполнится (короткое вычисление)
```

## Таблицы истинности (сводная)

### AND (&&)

| A     | B     | A && B |
|-------|-------|--------|
| true  | true  | true   |
| true  | false | false  |
| false | true  | false  |
| false | false | false  |

**Правило:** `true` только когда **оба** `true`

### OR (||)

| A     | B     | A \|\| B |
|-------|-------|----------|
| true  | true  | true     |
| true  | false | true     |
| false | true  | true     |
| false | false | false    |

**Правило:** `false` только когда **оба** `false`

### NOT (!)

| A     | !A    |
|-------|-------|
| true  | false |
| false | true  |

**Правило:** инверсия значения

### XOR (!=)

| A     | B     | A != B |
|-------|-------|--------|
| true  | true  | false  |
| true  | false | true   |
| false | true  | true   |
| false | false | false  |

**Правило:** `true` когда операнды **разные**

## Ключевые моменты

1. **Три основные операции:** `&&` (И), `||` (ИЛИ), `!` (НЕ)
2. **Бинарные операции:** `&&` и `||` принимают два операнда
3. **Унарная операция:** `!` принимает один операнд
4. **Результат всегда `bool`:** все логические операции возвращают булево значение
5. **XOR через неравенство:** `a != b` эквивалентно XOR
6. **Приоритет:** `!` > `&&` > `||`
7. **Скобки:** явно управляют порядком вычисления
8. **Короткое вычисление:** второй операнд может не вычисляться
9. **Тройные неравенства:** не поддерживаются, используйте `&&`
10. **Сравнение строк:** чувствительно к регистру

## Best Practices

### 1. Используйте скобки для ясности

```go
// ✅ Понятно
isValid := (age >= 18) && (hasLicense == true)

// ❌ Менее понятно
isValid := age >= 18 && hasLicense == true
```

### 2. Разбивайте сложные условия

```go
// ✅ Читаемо
isAdult := age >= 18
hasPermission := isAdmin || isOwner
canAccess := isAdult && hasPermission

// ❌ Сложно
canAccess := age >= 18 && (isAdmin || isOwner)
```

### 3. Именуйте промежуточные результаты

```go
// ✅ Самодокументируемый код
isWeekday := !isSaturday && !isSunday
isWorkingHours := hour >= 9 && hour <= 18
isBusinessTime := isWeekday && isWorkingHours

// ❌ Тяжело читать
isBusinessTime := (!isSaturday && !isSunday) && (hour >= 9 && hour <= 18)
```

### 4. Используйте короткое вычисление для безопасности

```go
// ✅ Безопасно: деление не выполнится при denominator == 0
if denominator != 0 && numerator/denominator > 10 {
    // ...
}

// ❌ Опасно: может быть паника при denominator == 0
if numerator/denominator > 10 && denominator != 0 {
    // ...
}
```

### 5. Избегайте избыточных сравнений

```go
isActive := true

// ✅ Лаконично
if isActive {
    // ...
}

// ❌ Избыточно
if isActive == true {
    // ...
}
```

## Что запомнить

- **`&&`** — оба `true` → результат `true`
- **`||`** — хотя бы один `true` → результат `true`
- **`!`** — инверсия значения
- **XOR** — используйте `!=`
- **Приоритет:** `!` → `&&` → `||`
- **Скобки** — явно управляют порядком
- **Короткое вычисление** — оптимизация и безопасность
- **Тройные неравенства** — не существуют в Go
- **Таблицы истинности** — справочник для всех операций
- **Читаемость** — важнее краткости
