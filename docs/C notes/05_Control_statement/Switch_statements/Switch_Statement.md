# Switch Statement

## Multi-Way Branching
**Jump table** for integer/constant expressions. Faster than chained if-else for many cases.

| Feature | Description | Rules |
|---------|-------------|-------|
| **Labels** | `case constant:` | Integer constants only |
| **fallthrough** | No `break` → next case | Intentional or bug |
| `default` | Catch-all (optional) | Like `else` |

## Syntax
```c
switch (expression) {
    case value1:
        statements;  // No break → fallthrough
    case value2:
        statements;
        break;       // Exit switch
    default:
        statements;
        break;       // Optional
}
```

## Examples
```c
#include <stdio.h>
int main() {
    int cmd = 2;
    
    // Basic menu
    switch (cmd) {
        case 1: printf("Add\n");     break;
        case 2: printf("Delete\n");  break;
        case 3: printf("Edit\n");    break;
        default: printf("Invalid\n"); break;
    }  // Output: Delete
    
    // Fallthrough example
    int level = 2;
    switch (level) {
        case 0: printf("No access\n"); 
        case 1: printf("Read only\n");
        case 2: 
        case 3: printf("Full access\n"); break;
        default: printf("Unknown\n");
    }  // Output: Full access (fallthrough from case 2)
    
    return 0;
}
```

**Output**:
```
Delete
Full access
```

## Execution Flow
```
1. Evaluate expression → integer value
2. Jump to matching case (or default)
3. Execute sequentially until break/return/end
4. Fallthrough = feature AND bug source
```

## Common Patterns
```c
// Enum handler
enum Op { ADD=1, SUB, MUL, DIV };
int op = MUL;
switch (op) {
    case ADD: result = a + b; break;
    case SUB: result = a - b; break;
    case MUL: result = a * b; break;
    case DIV: result = a / b; break;
    default:  result = 0;     break;
}

// ASCII processing
char c = 'A';
switch (c) {
    case 'A' ... 'Z': printf("Upper\n"); break;  // Range (C99)
    case 'a' ... 'z': printf("Lower\n"); break;
    default:         printf("Other\n");  break;
}
```

## Fallthrough Control
```c
// EXPLICIT fallthrough (C23)
switch (x) {
    case 1:
    case 2:
    case 3:  // Multiple cases → same handler
        process123();
        break;
    
    // C23: [[fallthrough]];  // Suppresses warning
}

// WRONG: Missing break
switch (menu) {
    case 1: printf("Option 1\n");
    case 2: printf("Option 2\n");  // Always prints!
    default: break;
}
```

## Advanced Features
```c
// No expression (C doesn't allow, use for(;;))
switch (0) {  // Constant expression OK
    case 0: printf("Always\n"); break;
}

// Multiple values per case (C11)
#define CASE_RETURN(c1,c2) case c1: case c2: return
switch (err) {
    CASE_RETURN(1,2);
    CASE_RETURN(3,4);
    default: handle_error();
}
```

## Pitfalls
```c
// VARIABLE in case → ERROR
int x = 5;
switch (cmd) {
    case x:  // ❌ Compile error
    case 5:  // OK
}

// FALLTHROUGH BUG
switch (op) {
    case '+': result = a+b;  // Missing break!
    case '-': result = a-b; break;  // Always subtract!
}

// UNREACHABLE
switch (x) {
    case 1: printf("One\n"); break;
    default: printf("Other\n"); break;
    case 2: printf("Two\n");   // Unreachable warning
}
```

## Pentest Relevance
- **Jump table overwrite**: Corrupt case table → RCE
- **Missing breaks**: `case ADMIN: case USER: grant_access();`
- **Type confusion**: `switch((char)big_int)` truncated cases
- **Parser bypass**: `switch(toupper(cmd))` case sensitivity
- **Dead cases**: Unused enum values → validation holes

*Lab: `int score=85`. Switch: 90-100="A", 80-89="B", 70-79="C", else="F". Handle fallthrough correctly.*