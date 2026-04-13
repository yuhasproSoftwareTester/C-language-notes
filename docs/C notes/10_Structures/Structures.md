# Structures

## **What is a Struct?**
**Struct** = **composite data type** grouping **heterogeneous data** under one name. Like a C++ class but **no methods**—just data.

```
Real-world: struct User {char name[32]; int age; char *email;}
Memory: Contiguous block with **padding** for alignment.
```

**Syntax**:
```c
struct Point {         // Tag name
    int x, y;          // Members
};

struct Point p = {10, 20};  // Initialization
```

## **Why Use Structs? (MUST Reasons)**
| Use Case | Without Structs | With Structs |
|----------|-----------------|--------------|
| **Data grouping** | `int x,y; char name[32];` | `struct Point {int x,y;};` |
| **Function params** | 5 separate args | Single `struct Rect r` |
| **Dynamic alloc** | Manual offset calc | `malloc(sizeof(Rect))` |
| **Hardware structs** | Magic numbers | `struct Registers {uint32_t eax;};` |
| **File formats** | Byte offsets | `fread(&header, sizeof(Header), 1, fp);` |

**Pentest Reality**: **90% of heap exploits target struct padding/misalignment**.

## **1. Declaration & Initialization**
| Style | Syntax | Notes |
|-------|--------|-------|
| **Tagged** | `struct Point {int x,y;}; struct Point p;` | Common, type-safe |
| **Typedef** | `typedef struct {int x,y;} Point; Point p;` | Shorter: `Point p` |
| **Anonymous** | `typedef struct {int x,y;} Point;` | GCC: no tag needed |
| **Partial init** | `struct Point p = {10};` | `y=0` (zero-init rest) |
| **Designated** | `struct Point p = {.y=20, .x=10};` | C99: order-independent |

```c
struct User {
    char name[32];     // Offset 0
    int age;           // Offset 32 (padding!)
    char *email;       // Offset 36
};  // Size: 40 bytes (8-byte alignment)
```

## **2. Accessing Members**
| Operator | Syntax | Use Case | Example |
|----------|--------|----------|---------|
| `.` **Direct** | `p.x = 10` | Local/struct var | `p.x += 5;` |
| `->` **Indirect** | `ptr->x = 10` | Pointer to struct | `*(ptr).x` equivalent |
| `offsetof` | `#include <stddef.h> offsetof(struct, member)` | Offset calc | `printf("%zu\n", offsetof(User, age)); // 32` |

```c
struct Point p = {10, 20};
struct Point *ptr = &p;

p.x = 99;        // Direct
ptr->x = 100;    // Indirect (dereference + member)
ptr->y++;        // Increments y
```

## **3. Memory Layout & Padding (EXPLOIT CRITICAL)**
**CPU Alignment Rule**: Members align to their size (int@4-byte, long long@8-byte).

```c
struct Packed {
    char a;    // 0-0 (1 byte)
    int b;     // 4-7 (pad 3 bytes!)
    char c;    // 8-8
};  // sizeof=12 bytes (not 6!)

struct Aligned {
    int x;     // 0-3
    char y;    // 4-4 (pad 3!)
    int z;     // 8-11
};  // sizeof=12 bytes
```

**Pentest Gold**: Padding bytes = **overflow targets** → next member corruption.

**Verify**:
```c
printf("sizeof(User): %zu\n", sizeof(struct User));
printf("offsetof(age): %zu\n", offsetof(struct User, age));
```

## **4. Arrays of Structs**
```c
struct User users[10] = {{"alice", 25}, {"bob", 30}};
// users[0].name, users[1].age
// Contiguous: users[1] == users + sizeof(User)
```

**2D Access**: `users[i].name[j]`

## **5. Nested Structs & Unions**
```c
struct Address {
    char street[64];
    int zip;
};

struct Employee {
    char name[32];
    struct Address addr;  // Embedded (offset calc)
    union {              // Memory overlap
        int id;
        char code[4];
    } identifier;
};
```

**Union Exploit**: Write `id=0x41414141` → `code="AAAA"` corruption.

## **6. Pointers to Structs (Common Pattern)**
```c
struct User *u = malloc(sizeof(struct User));
strcpy(u->name, user_input);  // Heap overflow → u->age corruption
free(u);                      // Dangling pointer
u->age = 1;                   // UAF → crash/exploit
```

## **7. Struct Exploitation Lab (REAL VULNS)**
```c
// vuln.c - gcc -fno-stack-protector -z execstack
struct Admin {
    char username[32];
    int is_admin;     // Offset 32
    char password[32]; // Offset 36
};

void authenticate(struct Admin *user) {
    gets(user->username);     // Overflow → is_admin=1
    if(user->is_admin)
        system("/bin/sh");
}

int main() {
    struct Admin admin = {"root", 0};
    authenticate(&admin);
}
```

**Exploit Payload**:
```
$ python3 -c 'print("A"*32 + "\x01\x00\x00\x00")' | ./vuln
# is_admin=1 → shell
```

**Precise Offset** (gdb):
```
(gdb) p &admin.is_admin - &admin.username
$1 = 32
```

## **8. Heap Struct Overflow**
```c
struct Data {
    int magic;         // 0xdeadbeef check
    char buffer[64];   // Overflow target
};

struct Data *d = malloc(sizeof(struct Data));
strcpy(d->buffer, long_input);  // Overwrite next heap chunk
```

**Tcache Poisoning**: Overwrite freed chunk's `fd` pointer → arbitrary alloc.

## **9. Common Struct Vulns**
| Vuln | Trigger | Impact | Mitigation |
|------|---------|--------|------------|
| **Padding Overflow** | `strcpy(s.name, long)` | Corrupt `s.age` | `__attribute__((packed))` |
| **UAF Struct** | `free(user); user->auth=1;` | Double-free RCE | Pointer validation |
| **Type Confusion** | `struct Admin *a = (Admin*)generic_user;` | Privilege esc | Size checks |
| **Offset Calc Bug** | `memcpy(user + 32, data, len)` | Wrong member | Use `offsetof()` |
| **Array Bounds** | `users[i].name` (i=999) | Remote code exec | Bounds checks |

## **10. Pro Tips & Debug**
```
sizeof(struct), offsetof()  → Map memory exactly
__attribute__((packed))     → Remove padding (embedded)
gdb: p/x &obj.field         → Hex offsets
pwndbg: vmmap, heap         → Visualize allocations
```

**Struct Hack** (Flexible Array Member C99):
```c
struct Header {
    int len;
    char data[];  // Must be last
};
struct Header *h = malloc(sizeof(Header) + 100);
```

**Next**: "Dynamic memory exploits (malloc/free)", "struct padding attacks", or "linked list manipulation"?