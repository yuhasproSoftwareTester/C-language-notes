# Functions

## **Core Mechanics**
Functions are **first-class blocks** with stack allocation. **Pass-by-value only** (pointers simulate reference).

```
Function Call Stack Frame:
High addr
+-------------------+
| Return Address    | ← Overwritten in overflow
+-------------------+
| Saved Base Ptr    |
+-------------------+
| Local Variables   | ← Buffer overflow target
+-------------------+
| Parameters        | (pushed right-to-left)
+-------------------+
Low addr
```

## **1. Declaration & Definition**
| Type | Syntax | Rules |
|------|--------|-------|
| **Prototype** (forward decl) | `int max(int a, int b);` | Semicolon, no body. Enables calls before definition. |
| **Definition** | `int max(int a, int b) { return a > b ? a : b; }` | Body `{}` required. Matches prototype exactly. |
| **No Proto Mismatch** | `int foo();` vs `int foo(int x)` | **UB**: Implicit `int` params (K&R legacy). Pentest: type confusion. |

**Multi-file Pattern**:
```c
// foo.h
int vulnerable(char *input);  // Prototype

// foo.c
int vulnerable(char *input) {  // Definition
    char buf[64];
    strcpy(buf, input);  // Overflow city
}
```

## **2. Calling Conventions**
**cdecl** (default on x86/Linux): Params **right-to-left**, caller cleans stack.

```c
void messy(int a, int b, int c) { }
messy(1, 2, 3);  // Stack: [3][2][1] | ret | locals
```

**Pentest Angle**: Predictable stack layout → precise overflow offsets.

| Call Style | Example | Stack Impact |
|------------|---------|--------------|
| **Normal** | `foo(42)` | Param → stack |
| **Array Decay** | `foo(arr)` | `arr` → `&arr[0]` pointer |
| **Function Pointer** | `int (*fp)(int) = foo; fp(42);` | ROP gadget chains |
| **Variadic** | `printf("%s", "hi");` | Format string hell |

## **3. Return Types (Comprehensive)**
| Return Type | Syntax | Pentest Relevance |
|-------------|--------|-------------------|
| **void** | `void print(char *s) { printf("%s\n", s); }` | No return value. Common in exploits (no EAX cleanup). |
| **int/char** | `int atoi(char *s)` | Truncates to 32-bit. Wraparound vulns. |
| **long/long long** | `long long sum(long a, long b)` | 64-bit safe, but legacy code mixes. |
| **float/double** | `double calc(double x)` | FPU stack exploits (rare). |
| **Pointer** | `char* strdup(char *s)` | NULL deref, UAF targets. |
| **Struct** | `struct Point {int x,y;}; struct Point get_point();` | **Hidden struct return** (caller allocates). |
| **Multi-value** | Use `struct` or pointers | `void get_minmax(int *min, int *max)` |

**Return Mechanics**:
```c
int foo() { return 42; }     // EAX = 42; RET
char* bar() { return "hi"; } // EAX = addr; RET (read-only data)
void baz() { RET }           // No value, just return
```

**Pentest: Return-Oriented Programming (ROP)**:
```
Overflow RET → gadget1 → gadget2 → system("/bin/sh")
Gadget: "POP RDI; RET" (param setup)
```

## **4. Parameter Passing (MUST KNOW)**
**Everything by value**. Arrays/strings → pointer decay.

| Param Type | Actual Push | Modifiable? | Vuln Class |
|------------|-------------|-------------|------------|
| `int x` | Copy of value | No (local copy) | N/A |
| `int *p` | Copy of pointer | Yes (via `*p`) | Deref null/bad ptr |
| `char buf[]` | `char*` (decays) | Yes | Buffer overflow |
| `char *buf` | `char*` pointer | Yes | Same |

```c
void overflow(char *input) {     // input = pointer copy
    char buf[32];
    strcpy(buf, input);          // *input → modifies caller's string
}

char victim[64] = "safe";
overflow("AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA");  // victim safe, buf dead
```

## **5. Advanced Function Features**
| Feature | Syntax | Exploit Vector |
|---------|--------|---------------|
| **Static** | `static int counter = 0;` | BSS persistence. Hard to spot. |
| **Inline** | `inline int square(int x)` | Code bloat, harder ASLR bypass. |
| **Recursion** | `int fact(int n)` | Stack exhaustion DoS. |
| **Varargs** | `int sum(int count, ...)` | Buffer overflow via unchecked args. |
| **Function Pointers** | `int (*action)(void) = system;` | DLL hijacking, hook bypass. |

**Variadic Deep Dive**:
```c
#include <stdarg.h>
int sum(int count, ...) {
    va_list args;
    va_start(args, count);
    int total = 0, i, n;
    for(i=0; i<count; i++) {
        n = va_arg(args, int);  // Read next arg
        total += n;
    }
    va_end(args);
    return total;
}
```
**Vuln**: No bounds → stack smash with crafted args.

## **6. Lab: Function Overflow Chain**
```c
// Compile: gcc -fno-stack-protector -z execstack -o vuln vuln.c
int check_auth(char *user, char *pass) {
    char buffer[64];
    strcpy(buffer, pass);           // Offset 0
    if(strcmp(user, "admin") == 0)  // Offset ~80
        strcpy(buffer, "welcome");  // Partial overwrite
    return strstr(buffer, "secret") != NULL;
}

int main() {
    char user[32], pass[32];
    gets(user); gets(pass);
    return check_auth(user, pass);
}
```

**Exploit** (precise offsets via gdb):
```
$ python3 -c 'print("A"*72 + "\x78\x56\x34\x12")' | ./vuln
# RET overwritten → control
```

## **Pentest Function Patterns**
1. **Proto Mismatch**: Implicit int → wrong sizes → overflows
2. **Varargs Abuse**: `printf(argv[1])` → format string RCE
3. **Recursion DoS**: `fib(-1)` →  stack overflow crash
4. **Hidden Lengths**: `strcpy(buf, read_exactly_100_bytes())`
5. **Function Ptr Overwrite**: Heap spray → `system(4194304)`

**Pro Tip**: `gdb -q ./vuln` → `run < payload` → `disas check_auth` → find RET offset.

**Next**: "pointer arithmetic exploits", "dynamic memory vulns", or "struct padding attacks"?