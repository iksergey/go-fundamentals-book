---
title: Чтение строк с пробелами через bufio
description: "bufio.Reader в Go: решение проблемы чтения строк с пробелами, метод ReadString('\\n'), strings.TrimSpace для удаления \\n и пробелов с краёв, импорт bufio/os/strings, пошаговое объяснение, сравнение с fmt.Scan, шаблон использования."
keywords: "bufio.Reader golang, ReadString \\n go, strings.TrimSpace, чтение строк с пробелами go, bufio.NewReader os.Stdin, проблема fmt.Scan пробелы, удаление \\n golang, ввод полной строки, многословные строки go"
lang: ru
parent: Указатели и продвинутый ввод данных
nav_order: 5
has_toc: false
---

# Чтение строк с пробелами через bufio

## Краткое описание

Решаем проблему чтения строк с пробелами. `fmt.Scan` воспринимает пробелы как разделители, поэтому используем `bufio.Reader` для чтения полной строки и `strings.TrimSpace` для очистки лишних символов.

## Проблема

```go
var fullName string
fmt.Scan(&fullName)
// Ввод: "John Doe"
// Результат: "John" ❌ (только до пробела)
```

## Решение: bufio.Reader

### Базовый код

```go
package main

import (
    "bufio"
    "fmt"
    "os"
    "strings"
)

func main() {
    reader := bufio.NewReader(os.Stdin)
    
    fmt.Print("Введите имя с пробелами: ")
    fullName, _ := reader.ReadString('\n')
    fullName = strings.TrimSpace(fullName)
    
    fmt.Printf("Результат: %s\n", fullName)
}
```

### Пошаговое объяснение

**Шаг 1: Создать reader**

```go
reader := bufio.NewReader(os.Stdin)
```

`bufio.NewReader(os.Stdin)` — создаёт читатель для стандартного ввода.

**Шаг 2: Прочитать строку**

```go
fullName, _ := reader.ReadString('\n')
```

`ReadString('\n')` — читает до символа переноса строки (Enter).

**Шаг 3: Убрать лишние пробелы и `\n`**

```go
fullName = strings.TrimSpace(fullName)
```

`strings.TrimSpace()` — удаляет пробелы и `\n` с начала и конца строки.

## Проблема с `\n`

Без `TrimSpace`:

```go
fullName, _ := reader.ReadString('\n')
fmt.Printf(">>%s<<\n", fullName)
// Ввод: "John Doe"
// Вывод: >>John Doe
//        <<  (перенос строки в конце!)
```

С `TrimSpace`:

```go
fullName = strings.TrimSpace(fullName)
fmt.Printf(">>%s<<\n", fullName)
// Ввод: "John Doe"
// Вывод: >>John Doe<<  ✅
```

## Что делает TrimSpace

Удаляет **все** пробельные символы с начала и конца:
- Пробелы
- Табуляции
- Переносы строк (`\n`)

```go
input := "   John Doe   \n"
clean := strings.TrimSpace(input)
// Результат: "John Doe"
```

## Полный пример

```go
package main

import (
    "bufio"
    "fmt"
    "os"
    "strings"
)

func main() {
    reader := bufio.NewReader(os.Stdin)
    
    fmt.Print("Введите полное имя: ")
    fullName, _ := reader.ReadString('\n')
    fullName = strings.TrimSpace(fullName)
    
    fmt.Print("Введите адрес: ")
    address, _ := reader.ReadString('\n')
    address = strings.TrimSpace(address)
    
    fmt.Printf("Имя: %s\n", fullName)
    fmt.Printf("Адрес: %s\n", address)
}
```

## Важные пакеты

```go
import (
    "bufio"   // Для bufio.NewReader
    "os"      // Для os.Stdin
    "strings" // Для strings.TrimSpace
)
```

## Ключевые моменты

**1. bufio.NewReader читает всю строку**

```go
reader := bufio.NewReader(os.Stdin)
input, _ := reader.ReadString('\n')
```

**2. ReadString возвращает два значения**

```go
text, err := reader.ReadString('\n')
// text — прочитанная строка
// err — ошибка (обычно игнорируем через _)
```

**3. TrimSpace обязателен**

Иначе `\n` останется в конце строки.

```go
text = strings.TrimSpace(text)  // Обязательно!
```

**4. Символ-разделитель — `'\n'`**

```go
reader.ReadString('\n')  // Читает до Enter
```

**5. Работает с любыми пробелами**

```go
// Ввод: "   много    пробелов   "
// После TrimSpace: "много    пробелов"
// (внутренние пробелы сохраняются)
```

## Сравнение методов

| Метод | Строки с пробелами | Простота |
|-------|-------------------|----------|
| `fmt.Scan` | ❌ Не работает | ✅ Просто |
| `bufio.Reader` | ✅ Работает | Средне |

## Шаблон использования

```go
// 1. Импорты
import (
    "bufio"
    "os"
    "strings"
)

// 2. Создать reader (один раз)
reader := bufio.NewReader(os.Stdin)

// 3. Читать строки (многократно)
text1, _ := reader.ReadString('\n')
text1 = strings.TrimSpace(text1)

text2, _ := reader.ReadString('\n')
text2 = strings.TrimSpace(text2)
```

## Что запомнить

- **`fmt.Scan`** не читает строки с пробелами
- **`bufio.NewReader(os.Stdin)`** — создать reader для ввода
- **`reader.ReadString('\n')`** — прочитать строку до Enter
- **`strings.TrimSpace()`** — удалить лишние пробелы и `\n`
- Всегда используйте **TrimSpace** после ReadString
- Reader можно создать **один раз** и использовать многократно
- Нужны три пакета: `bufio`, `os`, `strings`

## Итог

Для чтения строк с пробелами:

```go
reader := bufio.NewReader(os.Stdin)
input, _ := reader.ReadString('\n')
input = strings.TrimSpace(input)
```

Три строки кода решают проблему! 🎯
