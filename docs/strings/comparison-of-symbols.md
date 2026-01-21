---
title: Сравнение символов
description: "Сравнение символов в Go: руны как числа int32, диапазоны 'A'...'Z' 'а'...'я', проблема 'ё' и 'Ё' вне диапазона, unicode.IsLetter() универсальное решение, подсчёт букв цифр пробелов, IsDigit IsSpace IsUpper функции, коды Unicode, практические примеры валидации."
keywords: "сравнение символов golang, диапазоны символов go, 'A'..'Z' 'а'..'я', проблема 'ё' диапазон, unicode.IsLetter golang, коды Unicode символов, подсчёт букв, IsDigit IsSpace, руны числа int32, валидация символов go"
lang: ru
parent: Строки
nav_order: 9
has_toc: false
---

# Сравнение символов и практический пример

***

### 1. Основа: символы — это числа

**Строка = набор байт**  
**Байты = числа**  
**Числа можно сравнивать**

```go
char := 'A'
fmt.Printf("'A' = %d\n", char)  // 65 (Unicode code point)
```

**Руна** (символ) — это `int32`, поэтому поддерживает все операции сравнения.

***

### 2. Сравнение символов

```go
char := 'M'

if char >= 'A' && char <= 'Z' {
    fmt.Println("Заглавная латинская буква")
}

if char >= 'a' && char <= 'z' {
    fmt.Println("Строчная латинская буква")
}
```

**Работает:** Сравниваем **коды Unicode**.

***

### 3. Диапазоны символов

| Диапазон | Символы | Коды |
|----------|---------|------|
| `'A'...'Z'` | Заглавные латинские | 65–90 |
| `'a'...'z'` | Строчные латинские | 97–122 |
| `'А'...'Я'` | Заглавные кириллические | 1040–1071 |
| `'а'...'я'` | Строчные кириллические | 1072–1103 |
| `'0'...'9'` | Цифры | 48–57 |

***

### 4. Практический пример: подсчёт букв

```go
sentence := "ёHello, мир! 🌍"

fmt.Printf("Предложение: %s\n", sentence)
fmt.Printf("len(): %d байт\n", len(sentence))
fmt.Printf("Символов: %d\n", utf8.RuneCountInString(sentence))
```

**Вывод:**
```
Предложение: ёHello, мир! 🌍
len(): 26 байт
Символов: 14
```

***

### 5. Подсчёт букв

```go
letterCount := 0

for _, char := range sentence {
    if (char >= 'a' && char <= 'z') || (char >= 'A' && char <= 'Z') ||
       (char >= 'а' && char <= 'я') || (char >= 'А' && char <= 'Я') {
        letterCount++
    }
}

fmt.Printf("Букв: %d\n", letterCount)
```

**Вывод:**
```
Букв: 10
```

**Подсчитаны:** `H, e, l, l, o, м, и, р` — латиница и кириллица.

***

### 6. ⚠️ Проблема: неполное условие

#### Что не учтено?

```go
if (char >= 'a' && char <= 'z') || (char >= 'A' && char <= 'Z') ||
   (char >= 'а' && char <= 'я') || (char >= 'А' && char <= 'Я')
```

**Не учитывается:**
- `'ё'` и `'Ё'` — **вне диапазона** `'а'...'я'`
- Другие кириллические буквы: `'ї', 'є', 'і'` (украинские)
- Акцентированные символы: `'é', 'ñ', 'ü'`

***

#### Полный пример с `'ё'`

```go
sentence := "ёHello"

// ❌ Не найдёт 'ё'
letterCount := 0
for _, char := range sentence {
    if (char >= 'а' && char <= 'я') || (char >= 'А' && char <= 'Я') {
        letterCount++
    }
}
fmt.Println(letterCount)  // 0 (ё не в диапазоне!)
```

**Проблема:** `'ё'` (код 1105) **больше** `'я'` (код 1103).

***

### 7. Правильное решение: проверка `'ё'` и `'Ё'`

```go
letterCount := 0

for _, char := range sentence {
    if (char >= 'a' && char <= 'z') || (char >= 'A' && char <= 'Z') ||
       (char >= 'а' && char <= 'я') || (char >= 'А' && char <= 'Я') ||
       char == 'ё' || char == 'Ё' {  // ✅ Добавлено
        letterCount++
    }
}

fmt.Printf("Букв: %d\n", letterCount)
```

**Теперь:** `'ё'` учитывается.

***

### 8. Универсальное решение: `unicode.IsLetter()`

```go
import "unicode"

letterCount := 0

for _, char := range sentence {
    if unicode.IsLetter(char) {
        letterCount++
    }
}

fmt.Printf("Букв: %d\n", letterCount)
```

**Преимущества:**
- Работает для **любых** алфавитов (греческий, арабский, китайский)
- Учитывает **все** буквы Unicode
- Не нужно знать диапазоны

***

### 9. Другие полезные функции `unicode`

```go
import "unicode"

char := 'A'

unicode.IsLetter(char)  // true — буква
unicode.IsDigit(char)   // false — цифра
unicode.IsSpace(char)   // false — пробельный символ
unicode.IsUpper(char)   // true — заглавная
unicode.IsLower(char)   // false — строчная
unicode.ToUpper(char)   // 'A' — в верхний регистр
unicode.ToLower(char)   // 'a' — в нижний регистр
```

***

### 10. Сравнение подходов

| Подход | Плюсы | Минусы |
|--------|-------|--------|
| **Диапазоны** `'a'...'z'` | Быстро, явно | Не учитывает `'ё'`, акценты |
| **`unicode.IsLetter()`** | Универсально | Немного медленнее |

***

### 11. Практические примеры

#### Проверка пароля (только буквы и цифры)

```go
import "unicode"

func isValidPassword(password string) bool {
    for _, char := range password {
        if !unicode.IsLetter(char) && !unicode.IsDigit(char) {
            return false
        }
    }
    return true
}

fmt.Println(isValidPassword("Pass123"))   // true
fmt.Println(isValidPassword("Pass-123"))  // false (дефис)
```

***

#### Подсчёт типов символов

```go
text := "Hello, мир! 123"

letters, digits, spaces := 0, 0, 0

for _, char := range text {
    switch {
    case unicode.IsLetter(char):
        letters++
    case unicode.IsDigit(char):
        digits++
    case unicode.IsSpace(char):
        spaces++
    }
}

fmt.Printf("Букв: %d, Цифр: %d, Пробелов: %d\n", letters, digits, spaces)
// Букв: 8, Цифр: 3, Пробелов: 2
```

***

#### Извлечение только букв

```go
text := "Hello, мир! 123"
var result []rune

for _, char := range text {
    if unicode.IsLetter(char) {
        result = append(result, char)
    }
}

fmt.Println(string(result))  // "Helloмир"
```

***

### 12. Коды символов `'ё'`

```go
fmt.Printf("'а' = %d\n", 'а')  // 1072
fmt.Printf("'я' = %d\n", 'я')  // 1103
fmt.Printf("'ё' = %d\n", 'ё')  // 1105 — ВНЕ диапазона!
fmt.Printf("'Ё' = %d\n", 'Ё')  // 1025
```

**Проблема:** `'ё'` (1105) > `'я'` (1103), поэтому **не попадает** в `'а'...'я'`.

***

### 13. Итоги

✅ **Руны (символы) можно сравнивать** как числа  
✅ Диапазоны: `'A'...'Z'`, `'a'...'z'`, `'а'...'я'`, `'А'...'Я'`  
⚠️ **`'ё'` и `'Ё'` не входят** в диапазон `'а'...'я'`  
✅ Для полноты добавляйте `char == 'ё' || char == 'Ё'`  
✅ **`unicode.IsLetter()`** — универсальное решение  
✅ `unicode` пакет: `IsDigit`, `IsSpace`, `IsUpper`, `ToLower`  
✅ Сравнение работает через **Unicode code points**

**Ключевое правило:**
```go
// ❌ Неполное (пропускает 'ё')
if char >= 'а' && char <= 'я' { ... }

// ✅ Полное (вручную)
if (char >= 'а' && char <= 'я') || char == 'ё' { ... }

// ✅ Универсальное
if unicode.IsLetter(char) { ... }
```

**Практическая рекомендация:**
```go
import "unicode"

// Для любых алфавитов
for _, char := range text {
    if unicode.IsLetter(char) {
        // Обработка буквы
    }
}
```
