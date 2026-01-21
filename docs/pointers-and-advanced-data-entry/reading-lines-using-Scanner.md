---
title: Чтение строк через Scanner
description: "bufio.Scanner в Go: чтение строк с пробелами, автоматическое удаление \\n, методы Scan() и Text(), сравнение с bufio.Reader, использование strings.TrimSpace для очистки пробелов с краёв, шаблон работы со Scanner, практические примеры."
keywords: "bufio.Scanner golang, Scan Text go, чтение строк с пробелами, автоматическое удаление \\n, Scanner vs Reader, strings.TrimSpace, bufio.NewScanner os.Stdin, scanner.Scan() go, ввод строки golang, пробелы в строках"
lang: ru
parent: Указатели и продвинутый ввод данных
nav_order: 6
has_toc: false
---

# Чтение строк через bufio.Scanner

## Краткое описание

Альтернатива `bufio.Reader` для чтения строк с пробелами. `bufio.Scanner` проще в использовании и **автоматически убирает `\n`** в конце строки.

## Два способа чтения строк

### Способ 1: bufio.Reader (предыдущий)

```go
reader := bufio.NewReader(os.Stdin)
fullName, _ := reader.ReadString('\n')
fullName = strings.TrimSpace(fullName)  // Нужен TrimSpace!
```

### Способ 2: bufio.Scanner (новый)

```go
scanner := bufio.NewScanner(os.Stdin)
scanner.Scan()
fullName := scanner.Text()
// TrimSpace не обязателен (но можно добавить)
```

## Базовый пример

```go
package main

import (
    "bufio"
    "fmt"
    "os"
)

func main() {
    scanner := bufio.NewScanner(os.Stdin)
    
    fmt.Print("Введите имя с пробелами: ")
    scanner.Scan()
    fullName := scanner.Text()
    
    fmt.Printf("Результат: %s\n", fullName)
}
```

## Как работает Scanner

**Шаг 1: Создать scanner**

```go
scanner := bufio.NewScanner(os.Stdin)
```

**Шаг 2: Прочитать строку**

```go
scanner.Scan()  // Читает до Enter
```

**Шаг 3: Получить текст**

```go
text := scanner.Text()  // Получить прочитанную строку
```

## Преимущество Scanner

**Автоматически убирает `\n`:**

```go
scanner.Scan()
text := scanner.Text()
fmt.Printf(">>%s<<\n", text)
// Ввод: "John Doe"
// Вывод: >>John Doe<<  ✅ (нет \n в конце!)
```

**Сравните с Reader:**

```go
text, _ := reader.ReadString('\n')
fmt.Printf(">>%s<<\n", text)
// Вывод: >>John Doe
//        <<  ❌ (есть \n!)
```

## Пробелы в начале и конце

Scanner **сохраняет** пробелы с краёв:

```go
scanner.Scan()
text := scanner.Text()
// Ввод: "   John Doe   "
// Результат: "   John Doe   " (пробелы сохранены)
```

Если нужно убрать — используйте `TrimSpace`:

```go
scanner.Scan()
text := scanner.Text()
text = strings.TrimSpace(text)
// Результат: "John Doe" ✅
```

## Полный пример с TrimSpace

```go
package main

import (
    "bufio"
    "fmt"
    "os"
    "strings"
)

func main() {
    scanner := bufio.NewScanner(os.Stdin)
    
    fmt.Print("Введите имя: ")
    scanner.Scan()
    fullName := scanner.Text()
    fullName = strings.TrimSpace(fullName)
    
    fmt.Printf("Результат: >>>%s<<<\n", fullName)
}
```

**Ввод:** `   много пробелов   !   `  
**Вывод:** `>>>много пробелов   !<<<` (края очищены, внутренние пробелы остались)

## Сравнение Reader vs Scanner

| Особенность | Reader | Scanner |
|-------------|--------|---------|
| Создание | `bufio.NewReader(os.Stdin)` | `bufio.NewScanner(os.Stdin)` |
| Чтение | `ReadString('\n')` | `Scan()` |
| Получение | Сразу возвращает | `Text()` |
| `\n` в конце | ✅ Есть | ❌ Нет |
| TrimSpace | Обязателен | Опционален |

## Шаблон использования

```go
// 1. Импорты
import (
    "bufio"
    "os"
    "strings"  // Опционально для TrimSpace
)

// 2. Создать scanner (один раз)
scanner := bufio.NewScanner(os.Stdin)

// 3. Читать строки
scanner.Scan()
text1 := scanner.Text()

scanner.Scan()
text2 := scanner.Text()

// 4. Опционально: убрать пробелы с краёв
text1 = strings.TrimSpace(text1)
```

## Ключевые моменты

**1. Scanner автоматически убирает `\n`**

Не нужно вручную удалять перенос строки.

**2. Три шага работы**

```go
scanner := bufio.NewScanner(os.Stdin)  // Создать
scanner.Scan()                         // Прочитать
text := scanner.Text()                 // Получить
```

**3. TrimSpace опционален**

Но полезен для удаления лишних пробелов с краёв.

**4. Проще, чем Reader**

Меньше кода, нет необходимости обрабатывать `\n`.

**5. Работает с пробелами**

```go
// Ввод: "John Doe Smith"
// Результат: "John Doe Smith" ✅
```

## Что запомнить

- **`bufio.NewScanner(os.Stdin)`** — создать scanner
- **`scanner.Scan()`** — прочитать строку
- **`scanner.Text()`** — получить текст
- Scanner **автоматически убирает `\n`**
- `TrimSpace` нужен только для пробелов с краёв
- **Проще**, чем Reader
- Пробелы **внутри** строки сохраняются

## Рекомендация

**Для чтения строк с пробелами используйте Scanner** — он проще и удобнее:

```go
scanner := bufio.NewScanner(os.Stdin)
scanner.Scan()
text := scanner.Text()
text = strings.TrimSpace(text)  // Опционально
```

Готово! 🎯
