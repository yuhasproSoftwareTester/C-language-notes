# If-Else Statements

## Conditional Control Flow
**Branching** based on boolean conditions (non-0 = true, 0 = false). Single `if`, `else`, chained `else if`.

| Structure | Description | Example |
|-----------|-------------|---------|
| `if`      | Execute if true | `if (x) stmt;` |
| `if-else` | True → if, false → else | `if (x) stmt1; else stmt2;` |
| `else if` | Chain multiple conditions | `if (x>0) ... else if (x<0) ... else ...` |

### Syntax
```c
if (condition) {
    // true block
} else if (condition2) {
    // alternative
} else {
    // catch-all (optional)
}
```

## Examples
```c
#include <stdio.h>
int main() {
    int x = 10, y = -5, z = 0;
    
    // Basic if
    if (x > 0) {
        printf("Positive: %d\n", x);  // Prints
    }
    
    // if-else
    if (y > 0) {
        printf("Y positive\n");
    } else {
        printf("Y non-positive: %d\n", y);  // Prints
    }
    
    // Chained
    if (z > 0) {
        printf("Zero positive\n");
    } else if (z < 0) {
        printf("Zero negative\n");
    } else {
        printf("Zero exactly\n");  // Prints
    }
    
    // Single-line (no braces)
    if (x) printf("X truthy\n");  // Prints
    
    return 0;
}
```

**Output**:
```
Positive: 10
Y non-positive: -5
Zero exactly
X truthy
```

## Dangling Else Problem
```c
if (x > 0)
    if (y > 0)
        printf("Both positive\n");
    else  // Matches INNER if (y>0)
        printf("Y <= 0\n");
```
**Fix**: Braces or indentation clarity.

## Ternary Operator (?:)
**Compact if-else** (right-associative).
```c
int result = (x > 0) ? x : -x;  // abs(x)
printf("%d\n", x ? "Truthy" : "Falsy");  // String selection
```

## Common Patterns
```c
// Menu/switch-like
char op = 'A';
if (op == 'A') printf("Add\n");
else if (op == 'B') printf("Subtract\n");
else printf("Invalid\n");

// Guard clause (early return)
if (!ptr) return -1;  // Fail-fast
// ... main logic
```

## Side-Effect Pitfalls
```c
int x = 5;
if (x++ > 5) {}  // UNDEFINED: when does increment happen?
```
**Avoid** modifying in conditions—separate statements.

## Precedence
```
? :   Lowest (right-to-left)
```
Always parenthesize complex ternaries.

## Pentest Relevance
- **Input-controlled branches**: `if (user_input) system(cmd)`
- **Bypass auth**: `if (strlen(token) && validate(token))`
- **Race conditions**: Time-of-check vs time-of-use
- **Dead code**: Unreachable branches in obfuscated malware
- **Ternary exploits**: `bufsize = input ? 1024 : strlen(input)`

*Lab: Array `[10,0,20]`. Use if-else + short-circuit to sum non-zero elements (handle zero safely).*