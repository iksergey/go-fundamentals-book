---
title: Продвинутые операции со строками
description: "Пакет strings в Go: Split разделение строк, Contains Index поиск подстрок, Replace замена, Join объединение, TrimSpace очистка, ToLower/ToUpper регистр, EqualFold сравнение без учёта регистра, HasPrefix/HasSuffix проверка, Fields по пробелам, unicode проверка типа символов."
keywords: "strings пакет golang, strings.Split разделение, Contains Index поиск, Replace ReplaceAll, Join объединение строк, TrimSpace очистка, ToLower ToUpper регистр, EqualFold сравнение, HasPrefix HasSuffix, Fields пробелы, unicode.IsLetter golang"
lang: ru
parent: Строки
nav_order: 11
has_toc: false
---

# Пакет `strings`: продвинутые операции со строками

***

### 1. Проблема ручного парсинга

#### Задача: разбить CSV-строку на слова

```go
csv := "apple;banana;cherry;date"
```

**Цель:** Получить каждое слово отдельно.

***

#### Ручное решение (сложно)

```go
csv := "apple;banana;cherry;date"
words := []string{}
currentWord := ""

for _, char := range csv {
    if char == ';' {
        words = append(words, currentWord)
        currentWord = ""  // Обнуляем буфер
        continue
    }
    currentWord += string(char)
}

// Не забыть последнее слово
words = append(words, currentWord)

fmt.Println(words)  // [apple banana cherry date]
```

**Проблема:** Много кода для простой задачи.

***

### 2. Решение через `strings.Split`

```go
csv := "apple;banana;cherry;date"
words := strings.Split(csv, ";")

fmt.Println(words)  // [apple banana cherry date]
```

**Одна строка** вместо цикла!

***

#### Особенность: пустая строка в конце

```go
csv := "apple;banana;cherry;"  // Точка с запятой в конце

words := strings.Split(csv, ";")
fmt.Println(words)  // [apple banana cherry ""]
```

**Последний элемент — пустая строка**.

***

### 3. Основные функции `strings`

| Категория | Функции |
|-----------|---------|
| **Поиск** | `Contains`, `Index`, `LastIndex`, `Count` |
| **Проверка** | `HasPrefix`, `HasSuffix` |
| **Замена** | `Replace`, `ReplaceAll` |
| **Разделение** | `Split`, `Fields`, `Join` |
| **Регистр** | `ToLower`, `ToUpper` |
| **Очистка** | `TrimSpace`, `Trim`, `TrimLeft`, `TrimRight` |
| **Повторение** | `Repeat` |

***

### 4. Сравнение строк

#### Лексикографическое сравнение

```go
s1 := "apple"
s2 := "banana"
s3 := "apple"

fmt.Println(s1 == s3)  // true
fmt.Println(s1 == s2)  // false
fmt.Println(s1 < s2)   // true (лексикографически)
```

**Сравнение по Unicode кодам символов**.

***

#### Механизм сравнения

```
"мама" vs "маша"

Шаг 1: 'м' == 'м' → продолжить
Шаг 2: 'а' == 'а' → продолжить
Шаг 3: 'м' vs 'ш'
       код('м') < код('ш') → "мама" < "маша"
```

**Сравнение идёт по рунам**, пока не найдётся различие.

***

#### Регистр имеет значение

```go
s1 := "Hello"
s2 := "hello"

fmt.Println(s1 == s2)  // false — регистр учитывается
```

**Заглавные буквы имеют другие коды**:
```go
fmt.Println('H')  // 72
fmt.Println('h')  // 104
```

Поэтому `'H' < 'h'`.

***

#### Визуальная похожесть ≠ равенство

```go
s1 := "М"  // Кириллица
s2 := "M"  // Латиница

fmt.Println(s1 == s2)  // false
fmt.Printf("'М' (кириллица) = %d\n", 'М')  // 1052
fmt.Printf("'M' (латиница) = %d\n", 'M')   // 77
```

**Выглядят одинаково, но коды разные**!

***

### 5. Сравнение без учёта регистра

```go
hello1 := "Hello"
hello2 := "hello"

fmt.Println(hello1 == hello2)  // false

// ✅ Без учёта регистра
fmt.Println(strings.EqualFold(hello1, hello2))  // true
```

**`strings.EqualFold()`** — case-insensitive сравнение.

***

### 6. Поиск подстрок

#### `Contains` — проверка наличия

```go
text := "Go is awesome! Go is fast!"

fmt.Println(strings.Contains(text, "awesome"))  // true
fmt.Println(strings.Contains(text, "python"))   // false
```

***

#### `Index` — первое вхождение

```go
fmt.Println(strings.Index(text, "Go"))   // 0
fmt.Println(strings.Index(text, "is"))   // 3
fmt.Println(strings.Index(text, "xyz"))  // -1 (не найдено)
```

**Возвращает индекс** или `-1`.

***

#### `LastIndex` — последнее вхождение

```go
fmt.Println(strings.LastIndex(text, "Go"))  // 15
```

***

#### `Count` — количество вхождений

```go
fmt.Println(strings.Count(text, "Go"))  // 2
```

***

### 7. Префикс и суффикс

```go
url := "https://golang.org"

fmt.Println(strings.HasPrefix(url, "https"))  // true
fmt.Println(strings.HasSuffix(url, ".org"))   // true
```

**Применение:** Проверка расширений файлов, протоколов.

***

### 8. Замена подстрок

#### `Replace` — ограниченная замена

```go
original := "I love Python. Python is great!"

// Заменить первое вхождение
replaced := strings.Replace(original, "Python", "Go", 1)
fmt.Println(replaced)  // "I love Go. Python is great!"
```

**`n=1`** — заменить только первое вхождение.

***

#### `ReplaceAll` — замена всех вхождений

```go
replacedAll := strings.ReplaceAll(original, "Python", "Go")
fmt.Println(replacedAll)  // "I love Go. Go is great!"
```

***

### 9. Разделение строк

#### `Split` — по разделителю

```go
csv := "apple;banana;cherry;date"
parts := strings.Split(csv, ";")

fmt.Println(parts)  // [apple banana cherry date]
fmt.Println(len(parts))  // 4
```

***

#### `Fields` — по пробелам

```go
sentence := "Go is   awesome"
words := strings.Fields(sentence)

fmt.Println(words)  // [Go is awesome]
```

**Игнорирует множественные пробелы**.

***

### 10. Объединение строк

```go
fruits := []string{"apple", "banana", "cherry"}
joined := strings.Join(fruits, ", ")

fmt.Println(joined)  // "apple, banana, cherry"
```

**`Join`** — эффективнее, чем `+` в цикле.

***

### 11. Удаление пробелов

#### `TrimSpace` — края строки

```go
messy := "   Hello, World!   "
trimmed := strings.TrimSpace(messy)

fmt.Printf("До: \"%s\" (len=%d)\n", messy, len(messy))     // 20
fmt.Printf("После: \"%s\" (len=%d)\n", trimmed, len(trimmed))  // 13
```

***

#### `Trim`, `TrimLeft`, `TrimRight`

```go
dashes := "---Hello---"

fmt.Println(strings.Trim(dashes, "-"))       // "Hello"
fmt.Println(strings.TrimLeft(dashes, "-"))   // "Hello---"
fmt.Println(strings.TrimRight(dashes, "-"))  // "---Hello"
```

**Удаляет указанные символы**.

***

### 12. Изменение регистра

```go
mixed := "Hello, World!"

fmt.Println(strings.ToLower(mixed))  // "hello, world!"
fmt.Println(strings.ToUpper(mixed))  // "HELLO, WORLD!"
```

***

#### С `unicode` для рун

```go
r := 'Ж'

fmt.Println(unicode.IsUpper(r))     // true
fmt.Println(unicode.ToLower(r))     // 'ж'
```

***

### 13. Повторение строк

```go
pattern := "Go"
repeated := strings.Repeat(pattern, 5)

fmt.Println(repeated)  // "GoGoGoGoGo"
```

***

### 14. Проверка типа символов (`unicode`)

```go
testChars := []rune{'A', 'б', '5', ' ', '!'}

for _, ch := range testChars {
    fmt.Printf("'%c': ", ch)
    fmt.Printf("буква=%t, ", unicode.IsLetter(ch))
    fmt.Printf("цифра=%t, ", unicode.IsDigit(ch))
    fmt.Printf("пробел=%t, ", unicode.IsSpace(ch))
    fmt.Printf("пунктуация=%t\n", unicode.IsPunct(ch))
}
```

**Вывод:**
```
'A': буква=true, цифра=false, пробел=false, пунктуация=false
'б': буква=true, цифра=false, пробел=false, пунктуация=false
'5': буква=false, цифра=true, пробел=false, пунктуация=false
' ': буква=false, цифра=false, пробел=true, пунктуация=false
'!': буква=false, цифра=false, пробел=false, пунктуация=true
```

***

### 15. Итоги

✅ **`strings.Split`** — разделение строки в одну строку кода  
✅ **Сравнение строк** — лексикографически, по кодам рун  
✅ **`strings.EqualFold`** — сравнение без учёта регистра  
✅ Визуально похожие символы (кириллица/латиница) **не равны**  
✅ Богатый набор функций: поиск, замена, разделение, очистка  
✅ **`strings.Fields`** — автоматически убирает множественные пробелы  
✅ **`strings.Join`** — эффективное объединение  
✅ **`unicode`** — проверка типа символов

**Ключевое правило:**
```
Перед реализацией вручную → проверьте пакет strings
Вероятность: функция уже существует ≈ 90%
```

**Практический паттерн:**
```go
// ❌ Сложно вручную
for _, char := range csv {
    // много кода...
}

// ✅ Одна строка
words := strings.Split(csv, ";")
```

**Замечание:**  
Позже будет пакет `strings`, который сэкономит часы разработки.
