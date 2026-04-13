# While Loop

## Pre-Test Loop
**Tests condition BEFORE each iteration**. Zero or more executions. `while (condition) { body }`.

| Type | Description | Executions |
|------|-------------|------------|
| `while` | Test first, then body | 0+ (skips if false) |
| **Infinite** | `while(1)` or `while(!0)` | Manual break |

## Examples
```c
#include <stdio.h>
int main() {
    int i = 0;
    
    // Basic counter
    while (i < 5) {
        printf("%d ", i);
        i++;  // Critical: prevent infinite loop
    }
    printf("\n");  // Output: 0 1 2 3 4
    
    // Sum until zero
    int num, sum = 0;
    printf("Enter numbers (0 to stop):\n");
    while (scanf("%d", &num) == 1 && num != 0) {
        sum += num;
    }
    printf("Sum: %d\n", sum);
    
    // Character processing
    char buf[100] = "Hello";
    char *p = buf;
    while (*p) {      // Until null terminator
        printf("%c", *p++);
    }
    printf("\n");
    
    return 0;
}
```

**Output**:
```
0 1 2 3 4 
Hello
```

## Infinite Loop Patterns
```c
while (1) {           // Common idiom
    // read input
    if (quit) break;
    process();
}

while (!feof(file)) { // DANGER: off-by-one
    read_chunk();
}
```

## Loop Controls
| Statement | Effect |
|-----------|--------|
| `break` | Exit loop immediately |
| `continue` | Skip to next iteration |
| `return` | Exit function (and loop) |

```c
int i = 0;
while (i < 10) {
    if (i == 3) { i++; continue; }  // Skip 3
    if (i == 7) break;              // Exit at 7
    printf("%d ", i);
    i++;
}
// Prints: 0 1 2 4 5 6
```

## Common Pitfalls
```c
// INFINITE: no increment!
int i = 0;
while (i < 10) {
    printf("%d\n", i);
    // i++;  // Missing!
}

// OFF-BY-ONE: wrong condition
char *p = buf;
while (p < buf + 100) {  // May overrun null-term
    process(*p++);
}
```

## Precedence Warning
```c
int i = 0;
while (i++ < 5) {      // i=0(false?1),1(false?2),...,5(false?6)
    printf("%d ", i);
}
// Prints: 1 2 3 4 5 6  (surprise!)
```

## Pentest Relevance
- **Buffer overread**: `while(*buf++) process()` → past null
- **DoS infinite loops**: `while(1) sleep()` resource exhaustion
- **Input validation**: `while(fgets(line, SIZE, stdin))`
- **Parser vulns**: `while(!memcmp(buf, "magic", 5)) parse_header()`
- **Crypto keygen**: `while(--tries) hash++` timing attacks

*Lab: Array `[10,0,20,0,30]`. While loop to sum non-zero elements (stop at first zero, no short-circuit).*