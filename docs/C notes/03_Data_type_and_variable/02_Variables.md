# Variables in C

## Variable Declaration & Initialization
**Syntax**: `type name = value;` (init optional).

```c
int x;           // Declare (uninitialized = garbage)
int y = 42;      // Initialize
int a = 10, b;   // Multiple
char name[] = "Alice";  // Array (string)
```

**Rules**:
- Must declare **before use** (C89); anywhere in block (C99+).
- Names: Letters, digits, `_` (no start with digit; case-sensitive).

## Storage Classes
Control **lifetime**, **visibility**, and **initialization**.

| Class     | Keyword | Lifetime      | Default Value | Scope      | Example Use |
|-----------|---------|---------------|---------------|------------|-------------|
| `auto`    | (default) | Block         | Garbage      | Block     | Local vars |
| `register`| `register` | Block       | Garbage      | Block     | Fast access (optimizer hint) |
| `static`  | `static` | Program       | 0/`NULL`     | Block/File| Persistent locals |
| `extern`  | `extern` | Program       | N/A          | File      | Global sharing |

**Examples**:
```c
void func() {
    int local = 1;       // auto: dies on exit
    static int counter = 0;  // Persists: increments each call
    counter++;
    printf("Call #%d\n", counter);
}
```

**Global Variables** (file-scope):
```c
int global_var = 100;  // Accessible everywhere (avoid globals)
static int file_only = 200;  // Only this file
```

**Extern** (multi-file):
```c
// file1.c
int shared = 42;

// file2.c
extern int shared;  // Links to file1
```

## Scope & Lifetime
- **Block Scope**: `{ int x; }` – `x` invisible outside.
- **File Scope**: Globals outside functions.
- **Nested**: Inner shadows outer.
- **Lifetime**: When memory allocated/freed.

```c
int x = 10;  // File scope
void main() {
    int x = 20;  // Block scope (hides global)
    printf("%d\n", x);  // 20
}
```

## Variable-Length Arrays (VLA) - C99+
Dynamic size at runtime (stack-allocated).
```c
void func(int size) {
    int arr[size];  // VLA: size from param
    // Use like fixed array
}
```
**Note**: Not standard in C11+ strict; use `malloc` for portability.

## Qualifiers Revisited (Variable Context)
```c
const int MAX = 100;              // Can't modify
volatile int sensor;              // Don't optimize (hardware)
static const volatile int FLAG;   // All three!
```

## Lvalues vs Rvalues
- **Lvalue**: Addressable (e.g., `x = 5;`).
- **Rvalue**: Value (e.g., `5 = x;` invalid).

## Unnamed/Variadic (Advanced)
Anonymous unions/structs (C11):
```c
union { int i; float f; } u;  // Untagged
```

## Memory Layout
```
High addr
[ Stack: Locals, VLAs ]
[ Heap: malloc ]
[ Data: Globals initialized ]
[ BSS: Globals uninit (zeroed) ]
[ Code: Functions ]
Low addr
```

## Debugging Tips
- **Valgrind** (Kali): `valgrind ./prog` – Detect uninit vars, leaks.
- `gdb`: `print x` to inspect.

## Full Example: Storage Classes Demo
```c
#include <stdio.h>

int global = 100;  // File scope

void func() {
    auto int auto_var = 1;       // Explicit auto (rare)
    register int reg_var = 2;    // Optimizer candidate
    static int static_var = 0;   // Persists
    
    auto_var++; reg_var++; static_var++;
    printf("auto:%d reg:%d static:%d global:%d\n", 
           auto_var, reg_var, static_var, global);
}

int main() {
    func();  // 2 3 1 100
    func();  // 2 3 2 100  (static increments!)
    return 0;
}
```

## Pentest Relevance
- **Globals**: Predictable addresses for ROP.
- `static` locals: Persist across calls in loops.
- Storage overflows: Stack vs heap exploits.

## Uncovered Topics Summary
- **Arrays**: Next (pointer connection).
- **Pointers**: Core (addresses vars).
- **Dynamic Alloc**: `malloc/free`.
- **Structs/Unions**: Composites.

```java 

```
