# Assignment Operators

## Overview
**Assign values** to variables (Lvalues). Highest precedence after unary.

## Simple Assignment
| Operator | Description       | Example       |
|----------|-------------------|---------------|
| `=`      | Assign value      | `x = 5;`     |

## Compound Assignment (Arithmetic)
Shorthand: `var op= expr` ≡ `var = var op expr`.

| Operator | Full Form     | Example          | Equivalent     |
|----------|---------------|------------------|----------------|
| `+=`     | `+ =`         | `x += 3;`        | `x = x + 3;`  |
| `-=`     | `- =`         | `x -= 2;`        | `x = x - 2;`  |
| `*=`     | `* =`         | `x *= 4;`        | `x = x * 4;`  |
| `/=`     | `/ =`         | `x /= 2;`        | `x = x / 2;`  |
| `%=`     | `% =`         | `x %= 3;`        | `x = x % 3;`  |

## Examples
```c
#include <stdio.h>
int main() {
    int x = 10;
    
    x += 5;    // x = 15
    x -= 3;    // x = 12
    x *= 2;    // x = 24
    x /= 4;    // x = 6
    x %= 5;    // x = 1
    
    printf("Result: %d\n", x);  // 1
    return 0;
}
```

## Key Points
- **Right-Associative**: `x *= y += 1` → `x *= (y += 1)`.
- **Type Promotion**: Same as standalone arithmetic.
- **No Overflow Check**: Wraps silently.
- **Chaining**: `a = b = c = 0;` (right-to-left).

## Pentest Relevance
- Compound ops in loops: Off-by-one, overflow chains.
- Misuse: `if (x = 5)` assigns instead of compares.

