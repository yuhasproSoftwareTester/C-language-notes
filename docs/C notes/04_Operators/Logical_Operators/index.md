
## Logical Operators
**Boolean logic** on scalars (non-0 = true, 0 = false). Short-circuit evaluation.

| Operator | Description   | Example          | Result |
|----------|---------------|------------------|--------|
| `&&`     | AND           | `1 && 1` → `1`<br>`1 && 0` → `0` | True if **both** true |
| `\|\|`   | OR            | `0 \|\| 0` → `0`<br>`1 \|\| 0` → `1` | True if **any** true |
| `!`      | NOT (unary)   | `!0` → `1`<br>`!5` → `0` | Inverts (0→1, non-0→0) |

### Short-Circuit
- `&&`: Skip right if left false.
- `||`: Skip right if left true.
```c
int x = 0;
if (x && printf("Never prints\n")) {}  // Safe: skips printf
```

## Increment/Decrement Operators
**Unary**: Modify **and return** value. Pre vs Post.

| Operator | Pre           | Post          |
|----------|---------------|---------------|
| `++`     | `++x` → inc, **return new** | `x++` → **return old**, inc |
| `--`     | `--x` → dec, **return new** | `x--` → **return old**, dec |

### Examples
```c
#include <stdio.h>
int main() {
    int x = 5;
    
    printf("%d\n", ++x);  // 6 (pre: inc first)
    printf("%d\n", x++);  // 6 (post: return 6, then x=7)
    printf("%d\n", x);    // 7
    
    printf("%d\n", --x);  // 6 (pre-dec)
    printf("%d\n", x--);  // 6 (post: return 6, x=5)
    
    // Logical
    printf("%d\n", 1 && 0);   // 0
    printf("%d\n", !x);       // 0 (x=5 → true → !true=0)
    
    return 0;
}
```

**Output**:
```
6
6
7
6
6
0
0
```

## Side-Effect Warnings
```c
int x = 5;
int y = ++x * x++;  // UNDEFINED: order unspecified!
```
**Avoid** in expressions—separate statements.

## Precedence
```
! ++ --  Highest (right-to-left)
&&
||       Lowest
```

## Pentest Relevance
- `&&` guards in exploits (e.g., `argc && system("cmd")`).
- Inc/dec in loop vulns (off-by-one).
- Short-circuit bypasses (input-controlled).

