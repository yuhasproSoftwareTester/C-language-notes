# Relational Operators

## Overview
**Compare values**, return `1` (true) or `0` (false). Result is `int`.

| Operator | Description     | Example     | Result |
|----------|-----------------|-------------|--------|
| `==`     | Equal           | `5 == 5`    | `1`    |
| `!=`     | Not Equal       | `5 != 3`    | `1`    |
| `<`      | Less Than       | `3 < 5`     | `1`    |
| `>`      | Greater Than    | `5 > 3`     | `1`    |
| `<=`     | Less/Equal      | `5 <= 5`    | `1`    |
| `>=`     | Greater/Equal   | `3 >= 5`    | `0`    |

## Key Behaviors
- **Type Promotion**: Operands promoted to common type.
- **Floating-Point**: Beware precision (`0.1 + 0.2 == 0.3` → false).
- **Strings**: No direct compare—use `strcmp()`.

## Examples
```c
#include <stdio.h>
int main() {
    int a = 5, b = 3;
    
    printf("%d\n", a == b);  // 0
    printf("%d\n", a != b);  // 1
    printf("%d\n", a < b);   // 0
    printf("%d\n", a > b);   // 1
    printf("%d\n", a <= 5);  // 1
    printf("%d\n", a >= 6);  // 0
    
    return 0;
}
```

## Precedence
All same level, **left-to-right**. Chains invalid (`if (a < b > c)` ambiguous).

## Pentest Notes
- `==` timing attacks (constant-time compare needed).
- Float compares in parsers → bypasses.
