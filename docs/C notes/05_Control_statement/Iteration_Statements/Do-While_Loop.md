# Do-While Loop

## Post-Test Loop
**Executes body FIRST, tests condition AFTER**. Minimum **1 execution** always.

| Type | Description | Executions |
|------|-------------|------------|
| `do-while` | Body, test, repeat if true | 1+ (always runs once) |
| **Key diff** | Post-test vs while's pre-test | Use for "try until" |

## Syntax
```c
do {
    // body (always executes ≥1)
} while (condition);  // Semicolon REQUIRED
```

## Examples
```c
#include <stdio.h>
int main() {
    int i = 0;
    
    // Always prints once, even if i>=5 initially
    do {
        printf("%d ", i);
        i++;
    } while (i < 5);  // Output: 0 1 2 3 4 
    
    // Menu loop (perfect use case)
    char choice;
    do {
        printf("1=Add 2=Quit? ");
        choice = getchar();
        if (choice == '1') printf("Adding...\n");
    } while (choice != '2');
    
    // Validate input (retry until good)
    int num;
    do {
        printf("Enter >0: ");
        scanf("%d", &num);
    } while (num <= 0);
    printf("Got %d\n", num);
    
    return 0;
}
```

**Output**:
```
0 1 2 3 4 
1=Add 2=Quit? 1
Adding...
1=Add 2=Quit? 2
Enter >0: -5
Enter >0: 0
Enter >0: 42
Got 42
```

## Loop Controls
```c
int i = 0;
do {
    if (i == 2) { i++; continue; }  // Skip body for i=2
    if (i == 7) break;              // Early exit
    printf("%d ", i);
    i++;
} while (i < 10);
// Prints: 0 1 3 4 5 6 
```

## Critical Differences
```c
int x = 5;

// WHILE: skips entirely
while (x < 5) {
    printf("while: %d\n", x++);  // Never prints
}

// DO-WHILE: prints once
do {
    printf("do-while: %d\n", x++);  // Prints "do-while: 5"
} while (x < 5);                   // Then stops
```

## Common Patterns
```c
// Read until EOF/success
char buf[100];
do {
    if (!fgets(buf, sizeof(buf), stdin)) break;
    process(buf);
} while (1);

// Decrement to zero
int tries = 3;
do {
    printf("Try %d\n", tries);
} while (tries-- > 0);  // Prints 3,2,1
```

## Pitfalls
```c
// MISSING SEMICOLON = INFINITE
do {
    process();
while (1);  // ❌ No semicolon after do-while

// POST-INCREMENT TRICK
int i = 0;
do {
    printf("%d ", i);
} while (i++ < 3);  // Prints: 0 1 2 3 (test i=0,1,2,3)
```

## Pentest Relevance
- **Force execution**: 1 guaranteed run → init/code exec before check
- **Buffer overread**: `do { process(*buf++); } while(*buf);` → null skip
- **Validation bypass**: `do { exec_cmd(input); } while(validate(input));`
- **DoS**: Unbounded retry `do { sleep(1); } while(!timeout());`
- **Parser bombs**: `do { parse_header(); } while(has_more());`

*Lab: Array `[10,20,0,30]`. Do-while to sum until first zero (processes 10,20 then stops).*