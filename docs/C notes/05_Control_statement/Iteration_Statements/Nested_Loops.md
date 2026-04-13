# Nested Loops

## Multiple Loop Levels
**Loops inside loops** create Cartesian product. Outer × Inner iterations total.

| Structure | Total Iterations | Use Case |
|-----------|------------------|----------|
| 2 levels | outer × inner | 2D arrays, pairs |
| 3 levels | o1 × o2 × inner | 3D data, matrices |
| **Variable bounds** | Dynamic sizes | Jagged arrays |

## Basic Examples
```c
#include <stdio.h>
int main() {
    // 3x3 grid (9 total iterations)
    for (int row = 0; row < 3; row++) {
        for (int col = 0; col < 3; col++) {
            printf("(%d,%d) ", row, col);
        }
        printf("\n");
    }
    // Output:
    //(0,0) (0,1) (0,2) 
    //(1,0) (1,1) (1,2) 
    //(2,0) (2,1) (2,2) 
    
    // Matrix multiplication pattern
    int A[2][3] = {{1,2,3},{4,5,6}};
    for (int i = 0; i < 2; i++) {        // rows
        for (int j = 0; j < 3; j++) {    // cols
            printf("%d ", A[i][j]);
        }
        printf("\n");
    }
    return 0;
}
```

## Break/Continue Scope
```c
// continue → inner loop only
for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
        if (j == 1) continue;  // Skip col=1 only
        printf("%d ", j);
    }
    printf("\n(SKIP)");
}
// 0 2 
//(SKIP)0 2 
//(SKIP)0 2 

// break → inner loop only
for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
        if (i == 1 && j == 1) break;  // Exit inner only
        printf("%d ", j);
    }
    printf("\n");
}
// 0 1 2 
// 0 1 
// 0 1 2 
```

## Labeled Break (GCC)
```c
outer: for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
        if (i == 1 && j == 1) break outer;  // Exit BOTH
        printf("%d ", j);
    }
    printf("\n");
}
// 0 1 2 
// 0 1 
```

## Advanced Patterns
```c
// Diagonal traversal
for (int i = 0, j = 0; i < 3 && j < 3; i++, j++) {
    printf("(%d,%d) ", i, j);
}

// Jagged array
int rows = 3;
for (int i = 0; i < rows; i++) {
    int cols = 3 - i;  // Variable inner bound
    for (int j = 0; j < cols; j++) {
        printf("%dx%d ", i, j);
    }
    printf("\n");
}
// 0x0 0x1 0x2 
// 1x0 1x1 
// 2x0 
```

## Optimization: Swap Order
```c
// ROW-MAJOR (cache friendly)
for (int i = 0; i < ROWS; i++) {
    for (int j = 0; j < COLS; j++) {
        process(matrix[i][j]);
    }
}

// COLUMN-MAJOR (strided access)
for (int j = 0; j < COLS; j++) {
    for (int i = 0; i < ROWS; i++) {
        process(matrix[i][j]);
    }
}
```

## Pitfalls
```c
// INFINITE: inner controls outer var
for (int i = 0; i < 3; i++) {
    for (int j = 0; i < 3; j++) {  // Wrong condition var!
        printf("%d ", j);
    }
}

// OFF-BY-ONE in 2D
char grid[3][4];  // 3 rows, 4 cols
for (int i = 0; i <= 2; i++) {    // OK
    for (int j = 0; j <= 3; j++) {  // Overflow j=3!
        grid[i][j] = 0;
    }
}
```

## Pentest Relevance
- **2D buffer overflows**: `for(i=0;i<size;i++) for(j=0;j<size;j++) buf[i*10+j]=input;`
- **DoS O(n²)**: `for(i=0;i<input;i++) for(j=0;j<input;j++) heavy();`
- **Path traversal**: `for(i=0;i<depth;i++) path = path + "/../" + dir[i];`
- **Crypto matrix ops**: Double loop timing leaks
- **Image parser**: `for(y=0;y<height;y++) for(x=0;x<width;x++) decode_pixel();`

*Lab: 3x3 array. Nested for to find max value and its (row,col) position.*