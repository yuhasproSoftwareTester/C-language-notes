
## Purpose
Comments explain code, disable sections, or document logic. Ignored by compiler—purely for humans.

## Syntax

### Single-Line Comments
```c
#include <cstdio>
int main(){
// This is a single-line comment
  int x = 10;  // Inline comment after code
  printf("%d",x);
  return 0;
}
```

### Multi-Line Comments
```c
/*
 * This spans
 * multiple lines
 * Useful for headers or TODOs
 */
```

## Best Practices
- Use `//` for short notes; `/* */` for blocks.
- Comment **why** (not what): Explain intent, not obvious ops.
- Security Tip: Comment risky code (e.g., `// TODO: Add bounds check to prevent overflow`).

## Example
```c
#include <stdio.h>

/* 
 * Simple calculator
 * Handles add/subtract
 */

int main() {
    int a = 5, b = 3;
    printf("Sum: %d\n", a + b);  // Output: 8
    return 0;
}
```


