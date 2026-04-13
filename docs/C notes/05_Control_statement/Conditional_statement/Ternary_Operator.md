# Ternary Operator 

## Conditional Expression
**Compact if-else** replacement. Syntax: `condition ? true_expr : false_expr`. Returns value.

| Form | Description | Example |
|------|-------------|---------|
| `?:` | Eval condition, return true/false expr | `x > 0 ? x : -x` → `abs(x)` |
| **Right-associative** | Chains as `a ? b : c ? d : e` → `a ? b : (c ? d : e)` | |

## Examples
```c
#include <stdio.h>
int main() {
    int x = 10, y = -5, z = 0;
    
    // Basic absolute value
    printf("%d\n", x > 0 ? x : -x);  // 10
    
    // Max of two
    printf("%d\n", x > y ? x : y);   // 10
    
    // Zero-handling
    printf("%s\n", z ? "Non-zero" : "Zero");  // "Zero"
    
    // Nested (right-assoc)
    printf("%d\n", x > 0 ? x : y > 0 ? y : 0);  // 10
    
    // Assignment
    int result = x ? x * 2 : 0;
    printf("%d\n", result);  // 20
    
    return 0;
}
```

**Output**:
```
10
10
Zero
10
20
```

## Common Patterns
```c
// Default value
int len = input ? strlen(input) : 0;

// Status string
char* status = authenticated ? "OK" : "DENIED";

// Bounds clamp
int idx = val > 10 ? 10 : val < 0 ? 0 : val;

// Truthy/falsy selection
printf("%s\n", ptr ? "Valid" : "NULL");
```

## Chaining Rules
```c
// Correct nesting (right-assoc)
int a = x > 0 ? 1 : y > 0 ? 2 : 3;  // Matches: x>0 ? 1 : (y>0 ? 2 : 3)

// WRONG (needs parens)
int b = x > 0 ? 1 : y > 0 ? 2 : 3;  // Same as above ✓

// Left-assoc would be: ((x>0 ? 1 : y>0) ? 2 : 3) ❌
```

## Side-Effect Warnings
```c
int x = 5;
int y = x++ > 3 ? x : x--;  // UNDEFINED: inc/dec order!
```
**Avoid** mutations in ternary—separate statements.

## Precedence
```
? :   Lowest (right-to-left)
```
```c
// Needs parens for && with ?:
int safe = (x && y) ? func() : 0;  // ✓
// int unsafe = x && y ? func() : 0;  // → x && (y ? func() : 0) ❌
```

## Pentest Relevance
- **Buffer sizing**: `bufsize = input ? 1024 : strlen(input)` → OOB if `input` huge
- **Conditional ROP**: `gadget = canary_ok ? pop_rdi : nop_chain`
- **Auth bypass**: `access = role == "admin" ? 1 : 0` → type confusion
- **Format string**: `fmt = user_supplied ? "%s" : "%d"` → crash/exploit
- **Dead code elimination**: Obfuscated `key = debug ? real_key : fake_key`

*Lab: Given `int scores[3] = {85, 0, 92}`, use ternary to map: >80="A", >60="B", else="F". Print grades.*