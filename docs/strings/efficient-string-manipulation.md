---
title: Эффективная работа со строками
description: "strings.Builder в Go: эффективная конкатенация vs += в циклах, ускорение в 20-100 раз, методы WriteString WriteByte WriteRune, предварительное выделение Grow(), измерение производительности time.Now() time.Since(), практические примеры SQL HTML логов, когда использовать Builder."
keywords: "strings.Builder golang, эффективная конкатенация, += медленная, WriteString быстрая, производительность строк go, Grow предварительное выделение, time.Now time.Since, ускорение конкатенации, динамическая сборка строк, буфер строк golang"
lang: ru
parent: Строки
nav_order: 12
has_toc: false
---

# `strings.Builder`: эффективная работа со строками

***

### 1. Проблема конкатенации

#### Задача: собрать строку из миллиона чисел

```go
result := ""
for i := 1; i <= 1000000; i++ {
    result += fmt.Sprintf("%d,", i)
}
```

**Каждая итерация создаёт новую строку в памяти**!

***

### 2. Измерение времени выполнения

```go
start := time.Now()

// ... операции со строками ...

elapsed := time.Since(start)
fmt.Printf("Время: %v\n", elapsed)
fmt.Printf("Миллисекунд: %.3f ms\n", elapsed.Seconds()*1000)
```

**`time.Now()`** — засекаем время, **`time.Since()`** — вычисляем разницу.

***

### 3. Результаты тестирования конкатенации

| Итераций | Время | Комментарий |
|----------|-------|-------------|
| 1,000 | ~0 ms | Незаметно |
| 100,000 | ~1500 ms | 1.5 секунды |
| 1,000,000 | ~136,287 ms | **2 минуты 16 секунд**! |

**Вывод:** Конкатенация через `+=` **катастрофически медленная**.

***

### 4. Решение: `strings.Builder`

```go
var sb strings.Builder

for i := 1; i <= 1000000; i++ {
    sb.WriteString(fmt.Sprintf("%d,", i))
}

result := sb.String()
```

**Инициализация не требуется** — просто объявляем переменную.

***

### 5. Сравнение производительности

#### ❌ Способ 1: Обычная конкатенация

```go
start := time.Now()

result1 := ""
for i := 1; i <= 100000; i++ {
    result1 += fmt.Sprintf("%d,", i)  // Каждый раз новая строка!
}

elapsed1 := time.Since(start)

fmt.Printf("Время: %v\n", elapsed1)
fmt.Printf("Символов: %d\n", len(result1))
```

**Результат:** ~1500 ms (1.5 секунды).

***

#### ✅ Способ 2: `strings.Builder`

```go
start := time.Now()

var sb strings.Builder
for i := 1; i <= 100000; i++ {
    sb.WriteString(fmt.Sprintf("%d,", i))  // Добавляет в буфер
}
result2 := sb.String()  // Только в конце преобразуем

elapsed2 := time.Since(start)

fmt.Printf("Время: %v\n", elapsed2)
fmt.Printf("Символов: %d\n", len(result2))
```

**Результат:** ~70 ms.

***

### 6. Ускорение

```
Конкатенация: ~1500 ms
Builder:      ~70 ms

Ускорение: 1500 / 70 ≈ 21× быстрее!
```

**Для миллиона итераций:** с **136 секунд** (2 мин 16 сек) до **~700 ms**.

***

### 7. Почему `Builder` быстрее?

#### Конкатенация через `+=`

```
Итерация 1: выделить память под "1,"
Итерация 2: выделить память под "1,2," (копировать "1,")
Итерация 3: выделить память под "1,2,3," (копировать "1,2,")
...
Итерация N: выделить N байт (копировать N-1)
```

***

#### `strings.Builder`

```
1. Выделить буфер с запасом
2. Добавлять данные в буфер (без копирования)
3. При нехватке места — увеличить буфер
4. В конце — один раз преобразовать в строку
```

***

### 8. Основные методы `Builder`

```go
var sb strings.Builder

sb.WriteString("Hello")      // Добавить строку
sb.WriteByte('!')            // Добавить байт
sb.WriteRune('😀')           // Добавить руну

result := sb.String()        // Получить строку
length := sb.Len()           // Текущая длина
sb.Reset()                   // Очистить буфер
```

***

### 9. Практические примеры

#### Построение SQL-запроса

```go
var query strings.Builder

query.WriteString("SELECT * FROM users WHERE ")

conditions := []string{"age > 18", "country = 'US'", "active = true"}

for i, cond := range conditions {
    if i > 0 {
        query.WriteString(" AND ")
    }
    query.WriteString(cond)
}

sql := query.String()
// "SELECT * FROM users WHERE age > 18 AND country = 'US' AND active = true"
```

***

#### Форматирование логов

```go
var log strings.Builder

log.WriteString("[INFO] ")
log.WriteString(time.Now().Format("2006-01-02 15:04:05"))
log.WriteString(" - User logged in: ")
log.WriteString("john_doe")

fmt.Println(log.String())
// [INFO] 2025-12-19 02:31:00 - User logged in: john_doe
```

***

#### Генерация HTML

```go
var html strings.Builder

html.WriteString("<ul>\n")
items := []string{"Apple", "Banana", "Cherry"}
for _, item := range items {
    html.WriteString("  <li>")
    html.WriteString(item)
    html.WriteString("</li>\n")
}
html.WriteString("</ul>")

fmt.Println(html.String())
```

**Вывод:**
```html
<ul>
  <li>Apple</li>
  <li>Banana</li>
  <li>Cherry</li>
</ul>
```

***

### 10. Когда использовать `Builder`

| Сценарий | Используйте |
|----------|-------------|
| 2-3 конкатенации | `+` (допустимо) |
| Цикл с конкатенацией | **`Builder`** (обязательно) |
| Динамическая сборка строк | **`Builder`** |
| Множество `+=` в коде | **`Builder`** |
| Сотни/тысячи операций | **`Builder`** |

***

### 11. Оптимизация: предварительное выделение памяти

```go
var sb strings.Builder
sb.Grow(1000000)  // Зарезервировать 1 МБ

for i := 1; i <= 100000; i++ {
    sb.WriteString(fmt.Sprintf("%d,", i))
}

result := sb.String()
```

**`Grow(n)`** — резервирует память, избегая многократных расширений буфера.

***

### 12. Сравнение с другими подходами

```go
// ❌ Медленно
result := ""
for i := 0; i < 10000; i++ {
    result += "x"
}

// ✅ Хорошо
var sb strings.Builder
for i := 0; i < 10000; i++ {
    sb.WriteString("x")
}
result := sb.String()

// ✅ Идеально
var sb strings.Builder
sb.Grow(10000)
for i := 0; i < 10000; i++ {
    sb.WriteString("x")
}
result := sb.String()

// ✅ Альтернатива: strings.Repeat (для одинаковых строк)
result := strings.Repeat("x", 10000)
```

***

### 13. Итоги

✅ **Конкатенация `+=`** — медленная для циклов  
✅ **`strings.Builder`** — быстрая альтернатива  
✅ Ускорение в **20-100 раз** на больших данных  
✅ **Не требует инициализации** — просто `var sb strings.Builder`  
✅ Методы: `WriteString`, `WriteByte`, `WriteRune`, `String()`  
✅ **`Grow(n)`** — предварительное выделение памяти  
✅ Для миллиона итераций: **136 сек → 0.7 сек**

**Ключевое правило:**
```
1-2 конкатенации        → `+` допустимо
Цикл с конкатенацией    → strings.Builder обязательно
Тысячи операций         → strings.Builder + Grow()
```

**Практический паттерн:**
```go
// ❌ Никогда так в циклах
for i := 0; i < N; i++ {
    result += something  // медленно
}

// ✅ Всегда так
var sb strings.Builder
for i := 0; i < N; i++ {
    sb.WriteString(something)  // быстро
}
result := sb.String()
```

**Вывод:**  
При работе с динамическими строками **всегда используйте `strings.Builder`** — это критично для производительности.
