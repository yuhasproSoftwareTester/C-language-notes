# Data Types, Constants, Type Conversion & Checking

## Primitive Data Types
C types define **size** and **range** (platform-dependent, typically 64-bit).

| Type      | Size (bytes) | Range/Example                  | Usage |
|-----------|--------------|--------------------------------|-------|
| `char`    | 1            | -128 to 127 / 'A' (ASCII 65)   | Chars, bytes |
| `unsigned char` | 1     | 0 to 255                       | Bytes |
| `short`   | 2            | -32k to 32k                    | Small ints |
| `int`     | 4            | -2B to 2B (~ -2^31 to 2^31-1)  | Integers |
| `long`    | 8            | -9E18 to 9E18                  | Big ints |
| `long long` | 8         | Same as long (explicit)        | Portable big ints |
| `float`   | 4            | ±3.4E±38, 6 decimals           | Single precision |
| `double`  | 8            | ±1.7E±308, 15 decimals         | Double precision |
| `void`    | 0            | N/A                            | Generic/no type |

**Modifiers**: `signed` (default), `unsigned` (positive-only, doubles range), `short`, `long`.

**Example**:
```c
int x = 42;
unsigned int y = 42U;  // Literal suffix
double pi = 3.14159;
char c = 'A';
```

## Type Qualifiers
- `const`: Immutable (read-only).
- `volatile`: May change unexpectedly (hardware/multithreading).
- `static`: Local scope/lifetime or internal linkage.

## Constants (Literals)
Immutable values hardcoded.

| Type      | Notation              | Example         |
|-----------|-----------------------|-----------------|
| Integer   | Decimal, Hex, Octal   | `42`, `0x2A`, `052` |
| Float/Double | Decimal, Exp     | `3.14`, `1e-3` (0.001) |
| Char      | Single quotes         | `'A'`, `'\n'` (newline) |
| String    | Double quotes         | `"Hello"` (null-terminated array) |
| Enum      | User-defined          | `enum {RED, GREEN};` |

**Named Constants**:
```c
const int MAX = 100;     // Compile-time constant
#define PI 3.14159    // Preprocessor macro (no type checking)
```

## Type Conversion
**Automatic (Promotion)**: Smaller → Larger (no data loss).
```c
int i = 5;
float f = i;     // 5.0 (int → float)
double d = i;    // 5.0
```

**Explicit (Casting)**: Force conversion (risky).
```c
double d = 3.99;
int i = (int)d;  // 3 (truncates)
float f = (float)42;  // 42.0
```

**Arithmetic Promotion Rules**:
- `char/short` → `int`.
- `float` → `double` in ops.
- Mixed int/float → float.

**Pointer Casting** (Dangerous):
```c
int x = 42;
char* p = (char*)&x;  // View int as bytes (endianness matters)
```

## Checking Data Type/Size
No runtime `typeof`—use **preprocessor directives**.

### `sizeof` Operator
Returns **bytes** of type/variable.
```c
printf("int: %zu bytes\n", sizeof(int));      // ~4
printf("double: %zu\n", sizeof(double));      // 8
char buf[10];
printf("Array: %zu\n", sizeof(buf));          // 10
printf("Pointer: %zu\n", sizeof(int*));       // 8 (64-bit)
```

### `<limits.h>` & `<float.h>` Headers
Constants for min/max/precision.
```c
#include <limits.h>
#include <float.h>
printf("INT_MAX: %d\n", INT_MAX);     // 2147483647
printf("FLT_DIG: %d\n", FLT_DIG);     // 6 (float decimals)
```

### Runtime Type Checks (Trick)
Use unions or macros for simulation:
```c
#define typeof(var) _Generic((var), \
    int: "int", \
    double: "double", \
    default: "unknown" \
)
printf("%s\n", typeof(42));  // C11 _Generic (GCC/Clang)
```

## Full Example
```c
#include <stdio.h>
#include <limits.h>
#include <float.h>

int main() {
    const int MAX_USERS = 50;
    int x = 42;
    double pi = 3.14159;
    
    printf("Sizes: int=%zu, double=%zu\n", sizeof(int), sizeof(double));
    printf("Ranges: INT_MAX=%d, FLT_DIG=%d\n", INT_MAX, FLT_DIG);
    printf("Cast: %d -> %.0f\n", x, (double)x);
    
    return 0;
}
```

**Output** (64-bit):
```
Sizes: int=4, double=8
Ranges: INT_MAX=2147483647, FLT_DIG=6
Cast: 42 -> 42
```

## Pentest Notes
- **Integer Overflow**: `INT_MAX + 1` → wraparound exploits.
- `sizeof` for buffer sizing in exploits.
- Casting in shellcode (int → func ptr).

*Lab: Declare vars of each type, print sizes/ranges, cast int→float→int.*