# Nested If Statements

## Multi-Level Branching
**If statements inside other if blocks**. Creates decision trees. Use braces always.

| Pattern | Description | Risk |
|---------|-------------|------|
| **Nested if** | Inner if only runs if outer true | Dangling else |
| **Deep nesting** | 3+ levels common in validation | Readability |
| **Braces required** | Prevent ambiguity | Bugs |

## Examples
```c
#include <stdio.h>
int main() {
    int user_id = 100;
    int level = 3;
    int attempts = 2;
    
    // Basic nested
    if (user_id > 0) {                    // Outer: valid ID
        if (level >= 2) {                 // Inner: high enough level
            printf("Access granted\n");   // Prints
        }
    }
    
    // Triple nested (auth flow)
    if (user_id > 0) {
        if (attempts < 5) {              // Not locked out
            if (level == 3) {            // Admin only
                printf("Admin access\n");
            } else {
                printf("User access\n");
            }
        } else {
            printf("Too many attempts\n");
        }
    } else {
        printf("Invalid ID\n");
    }
    
    return 0;
}
```

**Output**:
```
Access granted
Admin access
```

## Dangling Else Problem
```c
// AMBIGUOUS - matches INNER if
if (user_id > 0)
    if (level >= 2)
        printf("Access\n");
    else      // ← Binds to level >= 2 !?
        printf("Low level\n");

// FIXED with braces
if (user_id > 0) {
    if (level >= 2) {
        printf("Access\n");
    } else {
        printf("Low level\n");
    }
}
```

## Common Patterns
```c
// Validation chain (fail-fast alternative)
if (user_id <= 0) {
    printf("Invalid ID\n");
} else if (attempts >= 5) {
    printf("Locked out\n");
} else if (level < 2) {
    printf("Insufficient level\n");
} else {
    printf("Success\n");  // All passed
}

// Deep validation (vulnerable to nesting bugs)
if (auth_token) {
    if (parse_token()) {
        if (verify_signature()) {
            if (check_expiry()) {
                grant_access();
            }
        }
    }
}
```

## Readability Fixes
```c
// BAD: Pyramid of doom
if (a) {
    if (b) {
        if (c) {
            if (d) action();
        }
    }
}

// BETTER: Early returns / guard clauses
if (!a) return;
if (!b) return;
if (!c) return;
if (!d) return;
action();

// BEST: Table-driven or switch
```

## Pentest Relevance
- **Bypass chains**: Each level separate vuln → full access
- **Input control**: `if (strlen(input)) if (!strcmp(input, "admin"))`
- **Race TOCTOU**: Check → nested action window
- **Dead paths**: Unreachable inner branches (static analysis)
- **Logic bombs**: `if (date==2025) if (admin) delete_files()`

