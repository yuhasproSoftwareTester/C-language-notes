# Input/Output in C: `printf` and `scanf`

## Overview
C uses **standard I/O functions** from `<stdio.h>` for console input/output. No built-in GUI—text-based, efficient for tools/scripts.

## Output: `printf`
**Formatted printing** to `stdout` (console).

### Basic Syntax
```c
printf("format_string", arg1, arg2, ...);
```

### Format Specifiers
| Specifier | Type       | Example                  |
|-----------|------------|--------------------------|
| `%d`/`%i` | `int`      | `printf("%d", 42);` → `42` |
| `%f`      | `float`    | `printf("%f", 3.14);` → `3.140000` |
| `%lf`     | `double`   | `printf("%lf", 3.14159);` |
| `%c`      | `char`     | `printf("%c", 'A');` → `A` |
| `%s`      | `char*` (string) | `printf("%s", "Hi");` → `Hi` |
| `%x`/`%X` | Hex int    | `printf("%x", 255);` → `ff` |
| `%p`      | Pointer    | `printf("%p", &x);` → `0x7ffd...` |

### Flags & Precision
- Width: `%10d` (right-pad to 10 chars).
- Precision: `%.2f` (2 decimal places).
- Left-align: `%-10d`.

**Example**:
```c
#include <stdio.h>
int main() {
    int age = 25;
    float pi = 3.14159;
    char name[] = "Alice";
    printf("Name: %s, Age: %d, Pi: %.2f\n", name, age, pi);
    // Output: Name: Alice, Age: 25, Pi: 3.14
    return 0;
}
```

## Input: `scanf`
**Formatted reading** from `stdin` (keyboard).

### Basic Syntax
```c
scanf("format_string", &var1, &var2, ...);  // & = address-of
```

- **Always use `&`** for non-strings (passes address).
- Returns **number of successful reads** (check for errors).

**Examples**:
```c
int age;
char name[50];  // Buffer for string
printf("Enter name and age: ");
scanf("%s %d", name, &age);  // No & for %s (array name = pointer)
printf("Hello %s, age %d\n", name, age);
```

### Common Inputs
| Input Type  | Code Example                      |                   |
| ----------- | --------------------------------- | ----------------- |
| Single int  | `scanf("%d", &x);`                |                   |
| Multiple    | `scanf("%d %d", &a, &b);`         |                   |
| String      | `scanf("%s", buf);`               | // Stops at space |
| Line (safe) | `fgets(buf, sizeof(buf), stdin);` |                   |

**Security Note**: `scanf("%s")` risks **buffer overflow**—limit with `scanf("%49s", buf);`.

## Advanced I/O Methods

### `puts` / `fputs` (Output Strings)
```c
char str[] = "Hello";
puts(str);              // Prints + auto \n
fputs(str, stdout);     // To specific stream, no \n
```

### `getchar` / `putchar` (Single Char)
```c
int ch = getchar();     // Reads one char (returns int)
putchar(ch);            // Outputs one char
```

### `gets` (Deprecated—Avoid!)
- Unsafe: No bounds. Use `fgets` instead.

### `fgets` / `fputs` (Safe Line I/O)
```c
char buf[100];
fgets(buf, sizeof(buf), stdin);  // Safe: limits to 99 chars + \n
fputs(buf, stdout);
```

### Streams
- `stdin`, `stdout`, `stderr` (FILE* pointers).
- Redirect: `./prog < input.txt > output.txt 2> errors.txt`

## Full Example: Interactive Calculator
```c
#include <stdio.h>
int main() {
    int a, b;
    printf("Enter two ints: ");
    fflush(stdout);
    if (scanf("%d %d", &a, &b) == 2) {  // Validate input
        printf("Sum: %d, Product: %d\n", a+b, a*b);
    } else {
        printf("Invalid input!\n");
    }
    return 0;
}
```

```c
#include <stdio.h>
int main(){
int a;
printf("hello");
fflush(stdout);
scanf("%d",&a);
printf("%d",a);
return 0;
}
```

