# For Loop

## Compact Counter Loop
**Traditional 3-part loop**: init; condition; update. Most common loop type.

| Part | Purpose | Executes |
|------|---------|----------|
| **init** | Setup (once before loop) | 1st time only |
| **condition** | Pre-test (like while) | Before each iteration |
| **update** | Post-body (each iteration) | After body, before condition |

## Syntax
```c
for (init; condition; update) {
    // body
}
```

## Examples
```c
#include <stdio.h>
int main() {
    // Basic 0-4
    for (int i = 0; i < 5; i++) {
        printf("%d ", i);  // 0 1 2 3 4
    }
    printf("\n");
    
    // Reverse
    for (int i = 4; i >= 0; i--) {
        printf("%d ", i);  // 4 3 2 1 0
    }
    printf("\n");
    
    // Sum array
    int arr[] = {10, 20, 30};
    int sum = 0;
    for (int i = 0; i < 3; i++) {
        sum += arr[i];
    }
    printf("Sum: %d\n", sum);  // 60
    
    // Nested loops
    for (int row = 0; row < 3; row++) {
        for (int col = 0; col < 3; col++) {
            printf("%d ", row * 3 + col);
        }
        printf("\n");
    }
    return 0;
}
```

**Output**:
```
0 1 2 3 4 
4 3 2 1 0 
Sum: 60
0 1 2 
3 4 5 
6 7 8 
```

## Execution Order
```
1. init (once)
2. test condition → false? exit
3. execute body
4. update
5. → back to 2
```

## Infinite/Alternative Forms
```c
// Infinite
for (;;) {              // while(1)
    if (done) break;
}

// While equivalent
int i = 0;
for (; i < 5; ) {      // No init/update
    printf("%d ", i++);
}

// Classic C (pre-C99)
int i;
for (i = 0; i < 5; i++) {
    printf("%d ", i);
}
```

## Loop Controls
```c
for (int i = 0; i < 10; i++) {
    if (i == 3) continue;  // Skip 3
    if (i == 7) break;     // Stop at 7
    printf("%d ", i);      // 0 1 2 4 5 6
}
```

## Common Patterns
```c
// Dual index
for (int i = 0, j = 9; i < j; i++, j--) {
    printf("%d %d\n", i, j);
}

// Pointer arithmetic
int arr[] = {1,2,3};
for (int *p = arr; p < arr + 3; p++) {
    printf("%d ", *p);
}

// Stride
for (int i = 0; i < 100; i += 10) {  // 0,10,20,...,90
    printf("%d ", i);
}
```

## Pitfalls
```c
// OFF-BY-ONE
for (int i = 0; i <= 5; i++) {  // i=0,1,2,3,4,5 (6 iterations)
    arr[i] = 0;  // Buffer overflow if arr[5] invalid!
}

// WRONG SCOPE (C89)
for (int i = 0; i < 5; i++) {}  // C89 error
int i; for (i = 0; i < 5; i++) {}  // OK

// UNSIGNED WRAP
for (unsigned i = 10; i >= 0; i--) {  // Infinite! (wraps to UINT_MAX)
    printf("%u ", i);
}
```

## Pentest Relevance
- **Buffer overflows**: `for(i=0; i<=size; i++) buf[i]=0;`
- **DoS**: `for(i=0; i<INT_MAX; i++) heavy_work();`
- **Parser loops**: `for(i=0; buf[i]; i++) parse(buf[i]);`
- **Crypto**: `for(i=0; i<1000000; i++) hash_block();` timing
- **Format string**: `for(i=0; i<count; i++) printf(fmt[i], data[i]);`

*Lab: Array `[10,0,20,30,0]`. For loop to sum until first zero (i<5 && arr[i]!=0).*