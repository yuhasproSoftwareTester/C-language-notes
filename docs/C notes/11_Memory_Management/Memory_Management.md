# Basic Memory Management

## **What is Memory? (The Big Picture)**
**Memory** = **RAM** where your program lives during execution. C gives **manual control** (unlike Java/Python).

```
Process Memory Layout (Low → High Address):
+--------------------+
| 0x0000     Code    | ← Executable instructions (.text)
|            (RO)    |
+--------------------+
|            Data    | ← Global/static vars (.data)
|            (RW)    |
+--------------------+
|            BSS     | ← Uninitialized globals (zeroed)
|            (RW)    |
+--------------------+
| 0x7f...    Heap    | ← Dynamic alloc ↑ (malloc grows up)
|            (RW)    |
+--------------------+
|                    | ← mmap() regions (shared libs)
+--------------------+
| 0x7fff...  Stack   | ← Local vars, function calls ↓ (grows down)
|            (RW)    |
+--------------------+
| 0xffff     Kernel  |
+--------------------+
```

## **Why Memory Management is CRITICAL**
| Without Manual Control | With Manual Control | Pentest Impact |
|------------------------|-------------------|---------------|
| Fixed arrays only | Dynamic sizing | Heap spraying |
| Memory leaks | Explicit free | DoS (exhaust heap) |
| No large data | malloc big buffers | ROP chains |
| Stack limited | Heap unlimited | Bypass stack canaries |

**99% of exploits** = **memory corruption** (overflow, UAF, double-free).

## **1. Static Memory (Automatic)**
| Type | Where | Lifetime | Example |
|------|-------|----------|---------|
| **Stack** | Function locals | Function scope | `int x[100]; char buf[64];` |
| **Global** | `.data`/BSS | Program lifetime | `int global=42; static int hidden;` |
| **String Lit** | Read-only data | Program lifetime | `char *s = "hello";` (don't modify!) |

**Stack Overflow**:
```c
void vuln() {
    char buf[64];
    gets(buf);  // Overflow → return address → RCE
}
```

## **2. Dynamic Memory Allocation (Heap)**
**Heap** = runtime-allocated memory. **You control size & lifetime**.

| Function | Prototype | Use Case | Zeroed? |
|----------|-----------|----------|---------|
| `malloc(size)` | `void* malloc(size_t)` | **Basic alloc** | **NO** (garbage) |
| `calloc(n, size)` | `void* calloc(size_t, size_t)` | **Zero-init** | **YES** (safer) |
| `realloc(ptr, size)` | `void* realloc(void*, size_t)` | **Resize** | Preserves data |
| `free(ptr)` | `void free(void*)` | **Dealloc** | **MANDATORY** |

## **3. malloc() - Basic Allocation**
```c
#include <stdlib.h>
int *arr = malloc(10 * sizeof(int));  // 40 bytes
if(!arr) { perror("malloc"); exit(1); }  // NULL check!

arr[0] = 42;
arr[9] = 99;

free(arr);  // MUST free or leak!
arr = NULL; // Prevent UAF
```

**Size Calc**:
```c
struct User { char name[32]; int age; };  // 36 bytes
struct User *u = malloc(sizeof(struct User));
```

## **4. calloc() - Safe Zero-Init**
```c
int *arr = calloc(10, sizeof(int));  // 40 bytes, ALL ZERO
// No need to memset() - already clean
```

**Pentest**: Uninit `malloc()` → info leak (previous data).

## **5. realloc() - Resize (DANGEROUS)**
```c
int *tmp = realloc(arr, 20 * sizeof(int));
if(!tmp) { free(arr); exit(1); }
arr = tmp;  // realloc may move memory!
```

**Vuln Pattern**:
```c
char *buf = malloc(64);
strcpy(buf, small);
buf = realloc(buf, 1024);  // Copy forgotten → leak
```

## **6. free() - Deallocation (CRITICAL RULES)**
```
✅ free(malloc())     // Single free
✅ free(calloc()) 
❌ double free()      → Heap corruption
❌ free(stack var)    → Invalid pointer
❌ use-after-free     → Dangling pointer
```

**Safe Pattern**:
```c
int *p = malloc(100);
if(p) {
    // use p
    free(p);
    p = NULL;  // Nullify (prevents UAF)
}
```

## **7. Memory Layout Visualization**
```
malloc(64) → returns 0x555...1000
Heap:
[header][user data 64B][footer] ← Chunk
          ^p points here

free(p) → [header:freed][user data][fd/back ptrs] ← Metadata exposed
```

## **8. Complete Safe Dynamic Example**
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct User {
    char name[32];
    int age;
};

struct User* create_user(const char *name, int age) {
    struct User *u = malloc(sizeof(struct User));
    if(!u) return NULL;
    
    strncpy(u->name, name, sizeof(u->name)-1);
    u->name[sizeof(u->name)-1] = '\0';  // Force null-term
    u->age = age;
    return u;
}

void free_user(struct User *u) {
    if(u) free(u);
}

int main() {
    struct User *alice = create_user("Alice", 25);
    printf("%s is %d\n", alice->name, alice->age);
    free_user(alice);
    return 0;
}
```

## **9. Memory Debug Commands**
```
valgrind ./prog    # Leak detection
valgrind --tool=memcheck

gdb:
(gdb) heap         # pwndbg: heap chunks
(gdb) x/64gx p     # Hex dump allocation
(gdb) info proc mappings  # Memory map
```

## **10. Pentest Memory Vulns (Lab Ready)**
| Vuln | Code | Exploit |
|------|------|---------|
| **Heap Overflow** | `strcpy(heap_buf, long)` | Next chunk metadata → tcache poison |
| **Use-After-Free** | `free(p); *p=42;` | Double-free → arbitrary alloc |
| **Double Free** | `free(p); free(p);` | Heap list corruption |
| **Uninit Use** | `malloc()` → `printf("%s", buf)` | Info leak |
| **Integer Overflow** | `malloc(n * huge)` | Too small buffer |

**Heap Overflow Lab**:
```c
char *buf1 = malloc(64);
char *buf2 = malloc(64);  // Adjacent
strcpy(buf1, "A"*100);    // Overwrite buf2 metadata
```

**UAF Lab**:
```c
char *secret = malloc(64);
free(secret);
strcpy(secret, user_input);  // Dangling → crash/exploit
```

## **11. Rules to Live By (Memorize)**
```
1. ALWAYS check malloc() != NULL
2. free() EXACTLY once per malloc()
3. NULL after free() (UAF defense)
4. sizeof(struct) not sizeof(pointer)
5. calloc() for structs (zero padding)
6. NEVER free(stack vars)
```

**Size Pitfall**:
```c
❌ char *p = malloc(100); strcpy(p, str);  // No sizeof!
✅ char buf[100]; strcpy(buf, str);
```

**Next**: "Heap exploitation techniques", "stack vs heap overflows", or "memory leak detection lab"?