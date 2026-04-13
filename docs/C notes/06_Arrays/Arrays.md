# Arrays

## Fixed-Size Contiguous Memory
**Collection of same-type elements**. No built-in methods—manual indexing `array[index]`.

| Type            | Declaration   | Size         | Access   |
| --------------- | ------------- | ------------ | -------- |
| **Fixed array** | `int arr[5];` | Compile-time | `arr[i]` |
| **VLA** (C99)   | `int arr[n];` | Runtime      | `arr[i]` |

## Declaration & Initialization
```c
#include <stdio.h>
int main() {
    // Fixed size
    int arr[5];           // Uninitialized (garbage)
    int zeros[5] = {0};   // All zeros
    int ones[] = {1,2,3}; // Size=3 (inferred)
    
    // Partial init → rest zero
    int mixed[5] = {10, 20};  // = {10,20,0,0,0}
    
    // 2D array
    int mat[2][3] = {
        {1,2,3},
        {4,5,6}
    };
    
    // Print array
    for (int i = 0; i < 5; i++) {
        printf("%d ", mixed[i]);  // 10 20 0 0 0
    }
    printf("\n");
    
    return 0;
}
```

## Key Properties
| Property | Value | Example |
|----------|-------|---------|
| **Size** | `sizeof(arr)/sizeof(arr[0])` | 5 elements |
| **Address** | `&arr[0] == arr` | Array name = pointer |
| **Decay** | `arr` → `int*` in functions | Loses size info |

```c
int arr[5];
printf("Size: %zu\n", sizeof(arr)/sizeof(arr[0]));  // 5
printf("Addr: %p %p\n", (void*)arr, (void*)&arr[0]); // Same
```

## Common Operations (Manual)
```c
// Copy array
int src[5] = {1,2,3,4,5};
int dst[5];
for (int i = 0; i < 5; i++) {
    dst[i] = src[i];
}

// Find max
int max = src[0];
for (int i = 1; i < 5; i++) {
    if (src[i] > max) max = src[i];
}

// Reverse
for (int i = 0, j = 4; i < j; i++, j--) {
    int tmp = src[i]; src[i] = src[j]; src[j] = tmp;
}
```

## Multi-Dimensional Arrays
```c
// 2D: row-major storage
int mat[2][3] = {{1,2,3},{4,5,6}};
printf("%d\n", *((mat[0])+1));  // 2 (pointer arithmetic)

// 3D
int cube[2][2][2] = {{{1,2},{3,4}}, {{5,6},{7,8}}};
printf("%d\n", cube[1][0][1]);  // 6

// Flattened access
int flat[6] = {1,2,3,4,5,6};
printf("%d\n", flat[0*3+1]);  // 2 (mat[0][1])
```

## Strings (Char Arrays)
```c
char str[10] = "Hello";  // = {'H','e','l','l','o','\0',...}
char buf[100];

// strlen equivalent
int len = 0;
while (str[len]) len++;  // 5

// strcpy equivalent
int i = 0;
while ((buf[i] = str[i])) i++;  // Copy with null
```

## Dynamic Arrays (Manual)
```c
#include <stdlib.h>
// Allocate
int *dyn = malloc(5 * sizeof(int));
if (!dyn) return -1;

// Use like static array
for (int i = 0; i < 5; i++) {
    dyn[i] = i;
}

// Free
free(dyn);
dyn = NULL;
```

## Pitfalls & Vulnerabilities
```c
// BUFFER OVERFLOW
int buf[10];
for (int i = 0; i <= 10; i++) {  // i=10 → overflow!
    buf[i] = 0;
}

// OFF-BY-ONE
char str[5];
strcpy(str, "Hello");  // Truncates OR overflow (UB)

// UNINIT ACCESS
int arr[5];
printf("%d\n", arr[0]);  // Garbage value!

// VLA stack overflow
void vuln(int n) {
    int huge[n*1000];  // Large n → stack smash
}
```

## Pentest Relevance
- **Classic overflows**: `gets(buf)` → `buf[100]`
- **Format strings**: `printf(arr[i])` → `%s`/ `%n`
- **Heap feng shui**: Array alloc → grooming
- **TOCTOU**: `size=read_size(); int buf[size]; read_into(buf);`
- **Magic bytes**: `if(arr[0]==0xdeadbeef)` validation bypass

*Lab: `int scores[5]={85,92,78,0,88}`. Find highest score and count zeros using loop.*