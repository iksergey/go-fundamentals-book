---
title: Операции сравнения
description: "Операции сравнения в Go: 6 операторов == != < > <= >= возвращают bool, сравнение int точное, float требует epsilon из-за погрешности 0.1+0.2≠0.3, приведение типов для сравнения, особые значения +Inf -Inf NaN, boolean только == !=, отрицательные числа на числовой прямой."
keywords: "операции сравнения golang, == != < > <= >=, bool результат, сравнение int float, epsilon погрешность, 0.1+0.2!=0.3, приведение типов, math.Abs epsilon, +Inf -Inf NaN, math.IsNaN, boolean сравнение, числовая прямая отрицательные"
lang: ru
parent: Условия и логика
nav_order: 1
has_toc: false
---

# Операции сравнения

## Краткое описание

Операции сравнения — фундамент любого языка программирования. Они позволяют программе анализировать значения переменных, сравнивать их и возвращать логический результат (`true` или `false`). В Go доступно **6 основных операторов сравнения**, работающих с разными типами данных, но всегда возвращающих `bool`.

## Шесть операторов сравнения

Go предоставляет следующие операторы:

| Оператор | Название | Описание | Пример |
|----------|----------|----------|--------|
| `==` | Равно | Проверяет равенство | `10 == 20` → `false` |
| `!=` | Не равно | Проверяет неравенство | `10 != 20` → `true` |
| `<` | Меньше | Проверяет строгое меньше | `10 < 20` → `true` |
| `>` | Больше | Проверяет строгое больше | `10 > 20` → `false` |
| `<=` | Меньше или равно | Проверяет меньше/равно | `10 <= 20` → `true` |
| `>=` | Больше или равно | Проверяет больше/равно | `10 >= 20` → `false` |

### Базовый пример

```go
a := 10
b := 20

fmt.Printf("a == b: %t\n", a == b)   // false
fmt.Printf("a != b: %t\n", a != b)   // true
fmt.Printf("a < b:  %t\n", a < b)    // true
fmt.Printf("a > b:  %t\n", a > b)    // false
fmt.Printf("a <= b: %t\n", a <= b)   // true
fmt.Printf("a >= b: %t\n", a >= b)   // false
```

## Результат операции — тип bool

**Ключевое правило:** результат любой операции сравнения — **логическое значение** типа `bool`.

```go
var result bool = (5 > 3)  // true
result2 := (10 == 20)      // false
result3 := (15 <= 30)      // true

fmt.Printf("Тип: %T, Значение: %t\n", result, result) // Тип: bool, Значение: true
```

### Можно сохранять результат

```go
var k int = 5
var l int = 5
var result1 bool

result1 = k == l              // Сохраняем результат сравнения
result2 := 10 > 20           // Краткая запись
result3 := 15 <= 30

fmt.Println(result1)  // true
fmt.Println(result2)  // false
fmt.Println(result3)  // true
```

## Сравнение целых чисел

### Базовое сравнение

Целые числа сравниваются **точно**, без потери точности.

```go
var num1 int = 100
var num2 int = 100
var num3 int = 200

fmt.Printf("num1 == num2: %t\n", num1 == num2) // true (одинаковые)
fmt.Printf("num1 == num3: %t\n", num1 == num3) // false (разные)
fmt.Printf("num1 < num3:  %t\n", num1 < num3)  // true
```

### Правило: сравниваем только одинаковые типы

**⚠️ ВАЖНО:** сравнивать можно **только** переменные одного типа.

```go
var int8Val int8 = 50
var int16Val int16 = 50

// fmt.Println(int8Val == int16Val)  // ❌ ОШИБКА КОМПИЛЯЦИИ!
// invalid operation: mismatched types int8 and int16
```

### Решение: явное приведение типов

```go
var int8Val int8 = 50
var int16Val int16 = 50

// ✅ Правильно: приводим к одному типу
fmt.Println(int16(int8Val) == int16Val)  // true

// Альтернатива
fmt.Println(int8Val == int8(int16Val))   // true (но риск переполнения!)
```

**Рекомендация:** приводите меньший тип к большему, чтобы избежать переполнения.

```go
var int8Val int8 = 50
var int32Val int32 = 50
var int64Val int64 = 50

// Приводим к большему типу
result1 := int64(int8Val) == int64Val    // ✅ Безопасно
result2 := int64(int32Val) == int64Val   // ✅ Безопасно
```

## Сравнение отрицательных чисел

Отрицательные числа сравниваются по правилам **числовой прямой**.

### Числовая прямая

```
    -∞ ← ... -20 ... -10 ... 0 ... 10 ... 20 ... → +∞
         меньше  ←     →  больше
```

**Правило:**
- Чем **правее** на прямой — тем **больше** число
- `-10 > -20` (потому что `-10` правее `-20`)

```go
negative1 := -10
negative2 := -5
negative3 := -10

fmt.Printf("-10 == -10: %t\n", negative1 == negative3)  // true
fmt.Printf("-10 < -5:   %t\n", negative1 < negative2)   // true (!)
fmt.Printf("-10 > -5:   %t\n", negative1 > negative2)   // false
```

**Объяснение:** `-10` меньше `-5`, потому что:
- `-10` находится **левее** на числовой прямой
- По модулю: `|-10| = 10` больше `|-5| = 5`, но **по значению** `-10 < -5`

## Сравнение вещественных чисел (float)

### Базовое сравнение

```go
float1 := 3.14
float2 := 3.14
float3 := 2.71

fmt.Printf("3.14 == 3.14: %t\n", float1 == float2)  // true
fmt.Printf("3.14 > 2.71:  %t\n", float1 > float3)   // true
fmt.Printf("3.14 < 2.71:  %t\n", float1 < float3)   // false
```

### Сравнение float32 и float64

Требует **явного приведения**, как и с `int`.

```go
var float32Val float32 = 1.23
var float64Val float64 = 1.23

// fmt.Println(float32Val == float64Val)  // ❌ ОШИБКА

// ✅ Правильно
fmt.Println(float64(float32Val) == float64Val)  // true
```

## Проблема точности float: почему 0.1 + 0.2 ≠ 0.3

### Классическая проблема

**⚠️ КРИТИЧНО:** float хранит числа в **двоичной системе**. Некоторые десятичные дроби невозможно точно представить в двоичной форме.

```go
value1 := 0.1
value2 := 0.2
sum := value1 + value2
expected := 0.3

fmt.Printf("0.1 + 0.2 == 0.3: %t\n", sum == expected) // false (!!!)
```

**Почему?** Посмотрим точное представление:

```go
fmt.Printf("0.1 = %.17f\n", value1)    // 0.10000000000000001
fmt.Printf("0.2 = %.17f\n", value2)    // 0.20000000000000001
fmt.Printf("sum = %.17f\n", sum)       // 0.30000000000000004
fmt.Printf("0.3 = %.17f\n", expected)  // 0.29999999999999999
```

**Результат:** числа **отличаются** на микроскопическом уровне!

```go
difference := sum - expected
fmt.Printf("Разница: %.20f\n", difference) // 0.00000000000000005551
```

### Другие проблемные случаи

```go
test1 := 0.1 + 0.1 + 0.1
test2 := 0.3

fmt.Printf("0.1 + 0.1 + 0.1 = %.17f\n", test1)  // 0.30000000000000004
fmt.Printf("0.3             = %.17f\n", test2)  // 0.29999999999999999
fmt.Printf("Равны? %t\n", test1 == test2)       // false
```

### Почему это происходит?

**Двоичная система:**
- Число `0.1` в двоичной системе = **бесконечная дробь** `0.0001100110011...`
- Компьютер **обрезает** её до конечного представления
- Возникает **погрешность округления**

**Это не баг Go** — это особенность **всех языков программирования** (Java, C#, JavaScript, Python).

## Сравнение float с epsilon (правильный способ)

### Что такое epsilon?

**Epsilon (ε)** — допустимая погрешность, **малое число**, определяющее, насколько числа могут отличаться, чтобы считаться "равными".

```go
const epsilon = 1e-3  // 0.001 (одна тысячная)
```

### Метод сравнения с epsilon

**Формула:** `|a - b| < ε`

```go
func almostEqual(a, b, epsilon float64) bool {
    return math.Abs(a - b) < epsilon
}
```

**Логика:**
1. Находим **разность** двух чисел: `a - b`
2. Берём **модуль** (абсолютное значение): `|a - b|`
3. Сравниваем с epsilon: если разность **меньше** epsilon → числа "равны"

### Визуализация

```
Числовая прямая:
    9.5      10.0      10.5
     |   ε    |    ε    |
     └────────┼─────────┘
         Δ (дельта)
```

Если число попадает в **дельту** `[10 - ε, 10 + ε]` → считаем его "равным" 10.

### Практический пример

```go
const epsilon = 1e-3  // 0.001

x := 0.1 + 0.2  // 0.30000000000000004
y := 0.3        // 0.29999999999999999

// ❌ Прямое сравнение
fmt.Printf("x == y: %t\n", x == y)  // false

// ✅ Сравнение с epsilon
difference := math.Abs(x - y)
fmt.Printf("Разница: %.20f\n", difference)  // 0.00000000000000005551
fmt.Printf("|x - y| < ε: %t\n", difference < epsilon)  // true
```

**Работает!** Потому что разница `0.0000...0055` **меньше** `0.001`.

### Выбор epsilon

| Применение | Рекомендуемый epsilon | Пример |
|------------|----------------------|--------|
| Финансовые расчёты | `1e-9` (0.000000001) | Деньги, цены |
| Научные вычисления | `1e-15` (макс. точность) | Физика, математика |
| UI координаты | `1e-6` (0.000001) | Графика, позиции |
| Общие задачи | `1e-3` или `1e-9` | По умолчанию |

```go
const epsilon = 1e-9  // Стандартный выбор для большинства задач
```

### Пример с ценами

```go
const epsilon = 1e-3

price1 := 10.01
price2 := 5.50
price3 := 4.51
total := price2 + price3  // 10.01 (возможна погрешность)

// Прямое сравнение может не сработать
fmt.Printf("total == price1: %t\n", total == price1)

// С epsilon
difference := math.Abs(total - price1)
fmt.Printf("|total - price1| < ε: %t\n", difference < epsilon)  // true
```

### Сравнение очень близких значений

```go
closeValue1 := 1.0000000001
closeValue2 := 1.0000000002

difference := math.Abs(closeValue1 - closeValue2)
fmt.Printf("Разница: %.15f\n", difference)  // 0.000000000100000

// С epsilon 1e-9
fmt.Printf("Равны (ε=1e-9)?  %t\n", difference < 1e-9)   // true

// С epsilon 1e-12 (более строгая точность)
fmt.Printf("Равны (ε=1e-12)? %t\n", difference < 1e-12)  // false
```

### Полный пример функции сравнения

```go
package main

import (
    "fmt"
    "math"
)

func almostEqual(a, b, epsilon float64) bool {
    return math.Abs(a-b) < epsilon
}

func main() {
    const epsilon = 1e-9
    
    x := 0.1 + 0.2
    y := 0.3
    
    if almostEqual(x, y, epsilon) {
        fmt.Println("Числа равны с точностью", epsilon)
    } else {
        fmt.Println("Числа не равны")
    }
}
```

### Важные замечания

**1. Epsilon — не универсальное решение**

Не нужен для:
- Сравнения с константами: `x == 0.0` (обычно безопасно)
- Целых чисел, преобразованных в float: `float64(5) == 5.0`

**2. Выбор epsilon зависит от задачи **

```go
// Финансы (копейки важны)
const epsilonMoney = 1e-9

// Графика (пиксель = достаточная точность)
const epsilonUI = 1e-6

// Научные расчёты (максимальная точность)
const epsilonScience = 1e-15
```

**3. Относительная vs абсолютная погрешность**

Для чисел **разного порядка** лучше **относительная** погрешность:

```go
func relativeEqual(a, b, tolerance float64) bool {
    diff := math.Abs(a - b)
    mean := math.Abs(a+b) / 2.0
    return (diff / mean) < tolerance
}
```

## Особые значения float: +Inf, -Inf, NaN

### Получение особых значений

Go предоставляет функции для работы с **математическими абстракциями**:

```go
import "math"

positiveInf := math.Inf(1)   // +∞ (плюс бесконечность)
negativeInf := math.Inf(-1)  // -∞ (минус бесконечность)
notANumber := math.NaN()     // NaN (not a number)
```

### Бесконечности: +Inf и -Inf

**Свойства:**
- **+Inf** больше **любого** числа
- **-Inf** меньше **любого** числа
- **+Inf == +Inf** → `true` (бесконечности равны сами себе)

```go
positiveInf := math.Inf(1)
negativeInf := math.Inf(-1)

fmt.Printf("+Inf > 1000000:  %t\n", positiveInf > 1000000)   // true
fmt.Printf("+Inf > 1e308:    %t\n", positiveInf > 1e308)     // true
fmt.Printf("-Inf < -1000000: %t\n", negativeInf < -1000000)  // true
fmt.Printf("-Inf < -1e308:   %t\n", negativeInf < -1e308)    // true
```

**На числовой прямой:**

```
    -∞  ← ... -20 ... 0 ... 20 ... → +∞
    ↑                              ↑
минус бесконечность       плюс бесконечность
```

### NaN (Not a Number)

**Что такое NaN?**
- Результат **неопределённых** операций
- Примеры: `0 / 0`, `√(-1)`, `∞ - ∞`

**⚠️ КРИТИЧЕСКОЕ СВОЙСТВО:** NaN **НЕ РАВЕН** даже самому себе!

```go
notANumber := math.NaN()

fmt.Printf("NaN == NaN: %t\n", notANumber == notANumber)  // false (!!!)
```

**Почему?**
- NaN можно получить **разными способами**
- `4 / 0` даёт NaN
- `40 / 0` тоже даёт NaN
- Но это **разные** "не-числа"

### Правильная проверка NaN

```go
notANumber := math.NaN()

// ❌ Неправильно
fmt.Println(notANumber == math.NaN())  // false (всегда)

// ✅ Правильно
fmt.Println(math.IsNaN(notANumber))    // true
```

### Сравнение с особыми значениями

```go
x := 10.0
positiveInf := math.Inf(1)
negativeInf := math.Inf(-1)
nan := math.NaN()

// Бесконечности ведут себя предсказуемо
fmt.Printf("10 < +Inf: %t\n", x < positiveInf)   // true
fmt.Printf("10 > -Inf: %t\n", x > negativeInf)   // true

// NaN не сравнивается ни с чем
fmt.Printf("10 == NaN: %t\n", x == nan)    // false
fmt.Printf("10 < NaN:  %t\n", x < nan)     // false
fmt.Printf("10 > NaN:  %t\n", x > nan)     // false
fmt.Printf("NaN == NaN: %t\n", nan == nan) // false
```

### Практические рекомендации

**1. Избегайте операций, создающих NaN**

```go
// ❌ Опасно
result := 0.0 / 0.0  // NaN

// ✅ Проверяйте деление на ноль
if denominator != 0 {
    result := numerator / denominator
}
```

**2. Проверяйте бесконечности**

```go
result := 1e308 * 2  // Может быть +Inf

if math.IsInf(result, 0) {
    fmt.Println("Результат бесконечен")
}
```

**3. В реальных задачах старайтесь избегать этих значений**

Они нужны в специализированных задачах (математика, физика), но в обычном коде лучше их обходить.

## Сравнение boolean значений

### Только два значения

```go
bool1 := true
bool2 := true
bool3 := false
```

### Доступные операции

**Только `==` и `!=`** — больше ничего не имеет смысла.

```go
fmt.Printf("true == true:   %t\n", bool1 == bool2)  // true
fmt.Printf("true == false:  %t\n", bool1 == bool3)  // false
fmt.Printf("true != false:  %t\n", bool1 != bool3)  // true
fmt.Printf("false == false: %t\n", bool3 == false)  // true
```

### Операторы <, >, <=, >= НЕ работают

```go
bool1 := true
bool3 := false

// fmt.Println(bool1 > bool3)  // ❌ ОШИБКА КОМПИЛЯЦИИ
// invalid operation: bool1 > bool3 (operator > not defined on bool)
```

**Почему?** Для `bool` нет понятия "больше" или "меньше" — есть только "равно" или "не равно".

### Сравнение результатов операций

Результаты сравнений — это тоже `bool`, их можно сравнивать между собой:

```go
result1 := (5 > 3)        // true
result2 := (10 == 10)     // true
result3 := (2 < 1)        // false

fmt.Printf("(5 > 3) == (10 == 10): %t\n", result1 == result2)   // true (оба true)
fmt.Printf("(5 > 3) == (2 < 1):    %t\n", result1 == result3)   // false
fmt.Printf("(5 > 3) != (2 < 1):    %t\n", result1 != result3)   // true
```

### Можно записывать в одну строку

```go
var val1 int = 10
var val2 int = 10

// Разбивка на два шага
result1 := (5 > 3)
result2 := (val1 == val2)
comparison := result1 == result2

// Всё в одну строку
temp := (5 > 3) == (val1 == val2)  // true

fmt.Println(temp)  // true
```

## Ключевые моменты

**1. Шесть операторов сравнения**

`==`, `!=`, `<`, `>`, `<=`, `>=`

**2. Результат всегда bool**

```go
result := (10 > 5)  // result имеет тип bool
```

**3. Сравниваем только одинаковые типы**

```go
// ❌ Ошибка
var a int8 = 10
var b int16 = 10
// fmt.Println(a == b)

// ✅ Правильно
fmt.Println(int16(a) == b)
```

**4. Отрицательные числа: -10 < -5**

Чем **правее** на числовой прямой — тем **больше**.

**5. Float: прямое сравнение ненадёжно**

```go
// ❌ Ненадёжно
fmt.Println(0.1 + 0.2 == 0.3)  // false

// ✅ Используйте epsilon
epsilon := 1e-9
fmt.Println(math.Abs((0.1+0.2) - 0.3) < epsilon)  // true
```

**6. Epsilon для float **

```go
const epsilon = 1e-9  // Стандартный выбор

func almostEqual(a, b float64) bool {
    return math.Abs(a-b) < epsilon
}
```

**7. Особые значения**

- **+Inf** > любое число
- **-Inf** < любое число
- **NaN ≠ NaN** (всегда `false`)
- Используйте `math.IsNaN()` для проверки NaN

**8. Boolean: только == и !=**

```go
fmt.Println(true == false)  // ✅ Работает
// fmt.Println(true > false)  // ❌ Ошибка
```

**9. Результаты сравнения можно сравнивать**

```go
temp := (5 > 3) == (10 == 10)  // true == true → true
```

**10. Точность float32 vs float64**

`float64` предпочтительнее — выше точность.

## Best Practices

### 1. Приводите к большему типу

```go
var int8Val int8 = 50
var int64Val int64 = 50

// ✅ Безопасно
result := int64(int8Val) == int64Val

// ❌ Риск переполнения
// result := int8Val == int8(int64Val)
```

### 2. Используйте epsilon для float

```go
const epsilon = 1e-9

func compareFloat(a, b float64) bool {
    return math.Abs(a-b) < epsilon
}
```

### 3. Проверяйте особые значения

```go
result := someMathOperation()

if math.IsNaN(result) {
    // Обработка ошибки
}

if math.IsInf(result, 0) {
    // Обработка бесконечности
}
```

### 4. Избегайте сравнения float без epsilon в критичных местах

```go
// ❌ Плохо для финансов
if price1 + price2 == totalPrice {
    // ...
}

// ✅ Хорошо
const epsilon = 1e-9
if math.Abs((price1 + price2) - totalPrice) < epsilon {
    // ...
}
```

### 5. Для денег используйте библиотеки

```go
// Вместо float64 для денег
import "github.com/shopspring/decimal"

price := decimal.NewFromFloat(19.99)
total := price.Mul(decimal.NewFromInt(3))
```

## Что запомнить

- **6 операторов:** `==`, `!=`, `<`, `>`, `<=`, `>=`
- Результат — **всегда `bool`**
- Сравниваем **только одинаковые типы** (нужно приведение)
- **`int`** сравнивается точно
- **`float`** требует epsilon из-за погрешности
- **0.1 + 0.2 ≠ 0.3** (особенность float)
- **Epsilon:** `|a - b| < ε` для сравнения float
- **+Inf** > всего, **-Inf** < всего
- **NaN ≠ NaN** (используйте `math.IsNaN`)
- **`bool`:** только `==` и `!=`
- Отрицательные числа: `-10 < -5`
- Можно сравнивать результаты сравнений

## Полезные ссылки

- [Go float comparison on Stack Overflow](https://stackoverflow.com/questions/47969385/go-float-comparison)
- [Testing floating point numbers in Go](https://dev.to/juliaferraioli/testing-in-go-testing-floating-point-numbers-4i0a)
- [Understanding Floating-Point Numbers in Go](https://leapcell.io/blog/understanding-floating-point-numbers-in-go)
- [IEEE 754 Infinity and NaN](https://ftp.gnu.org/old-gnu/Manuals/glibc-2.2.3/html_node/libc_397.html)
