# Operators in C

## Overview
Operators perform operations on **operands** (variables/values). C has **45+ operators** grouped by type/priority.

**Priority**: Higher binds first (e.g., `*` before `+`). Use `()` to override.

## Categories (High-Level)
| Type          | Examples              |
|---------------|-----------------------|
| Arithmetic    | `+ - * / %`          |
| Relational    | `< > <= >=`          |
| Logical       | `&& \|\| !`          |
| Bitwise       | `& \| ^ ~ << >>`     |
| Assignment    | `= += -= *=`         |
| Other         | `? :` `sizeof` `. ->`|

**Focus**: **Arithmetic Operators Only** (as requested).

## Arithmetic Operators
Perform math on numerics (`int`, `float`, etc.). Promote types automatically.

| Operator | Name          | Example             | Result      | Notes |
|----------|---------------|---------------------|-------------|-------|
| `+`      | Addition      | `5 + 3`             | `8`         | - |
| `-`      | Subtraction   | `5 - 3`             | `2`         | Unary `-x` (negate) |
| `*`      | Multiplication| `5 * 3`             | `15`        | - |
| `/`      | Division      | `5 / 2`             | `2` (int!)  | Truncates toward 0 (ints) |
| `%`      | Modulo        | `5 % 2`             | `1`         | Remainder (ints only) |

### Key Behaviors
- **Integer Division**: `7/2 = 3` (discard fraction).
- **Floating-Point**: `7.0/2 = 3.5`.
- **Modulo**: Only integers; `a % b` = remainder of `a/b`. Undefined if `b=0`.
- **Overflow**: Wraps around (e.g., `INT_MAX + 1 = INT_MIN`).

### Examples
```c
#include <stdio.h>
int main() {
    int a = 7, b = 3;
    printf("Add: %d\n", a + b);    // 10
    printf("Sub: %d\n", a - b);    // 4
    printf("Mul: %d\n", a * b);    // 21
    printf("Div: %d\n", a / b);    // 2 (int trunc)
    printf("Mod: %d\n", a % b);    // 1
    
    double f = 7.0 / 3;            // 2.333...
    printf("Float Div: %.2f\n", f);
    
    return 0;
}
```

### Unary Arithmetic
```c
int x = 5;
printf("%d\n", -x);  // -5 (negation)
printf("%d\n", +x);  // 5 (identity)
```

## Operator Precedence (Arithmetic Only)
```
()  Highest
Unary + - 
* / % 
+ -  Lowest
```
**Example**: `5 + 3 * 2 = 11` (mul first).

## Pentest Notes
- **Integer Overflow**: `INT_MAX + 1` → exploits (CVE root cause).
- Modulo 0: Crash (DoS).
- Div trunc: Off-by-one vulns.

*Lab: Write program with two user-input ints; compute/perform all 5 ops, handle div-by-zero.*