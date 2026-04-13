# C Standard Library Functions: Beginner Essentials

## **Core Headers & Their Purpose**
| Header | Purpose | Key Functions |
|--------|---------|---------------|
| `stdio.h` | **Input/Output** | `printf`, `scanf`, `fopen`, `gets` (VULN!) |
| `string.h` | **String ops** | `strcpy`, `strlen`, `strcmp` (MOST VULNS HERE) |
| `stdlib.h` | **General utils** | `malloc`, `atoi`, `system`, `exit` |
| `math.h` | **Math ops** | `sqrt`, `pow`, `sin`, `floor` |
| `stdbool.h` | **Boolean type** | `true`, `false`, `bool` |
| `stdint.h` | **Fixed-width ints** | `uint32_t`, `int64_t` |
| `ctype.h` | **Char classification** | `isalpha`, `isdigit`, `toupper` |
| `time.h` | **Time functions** | `time`, `srand`, `rand` |

## **1. stdio.h - Input/Output (MUST MASTER)**
| Function | Prototype | Use | DANGER |
|----------|-----------|-----|--------|
| `printf(fmt, ...)` | `int printf(const char*, ...)` | Formatted output | Format string vulns (`%x %n`) |
| `scanf(fmt, &var)` | `int scanf(const char*, ...)` | Formatted input | `%s` NO BOUNDS → overflow |
| `gets(buf)` | `char* gets(char*)` | Line input | **NEVER USE** - infinite input |
| `fgets(buf, n, stdin)` | `char* fgets(char*, int, FILE*)` | **SAFE** line input | Bounds checked |
| `fopen(path, mode)` | `FILE* fopen(const char*, const char*)` | File open | `"r"`, `"w"`, `"a"` modes |
| `fread/fwrite` | `size_t fread(void*, size_t, size_t, FILE*)` | Binary I/O | Structs, network data |
| `putchar/getchar` | `int putchar(int)` | Single char | Fast I/O |

**Format Specifiers**:
```
%d %i     → int
%s        → string
%p        → pointer (hex)
%x %X     → hex (exploit!)
%f        → float
%lld      → long long
%n        → writes arg count (EXPLOIT!)
```

**Vuln Example**:
```c
printf(user_input);  // "%x%x%x%x" → stack dump
```

## **2. string.h - String Operations (EXPLOIT HEAVEN)**
| Function | Prototype | Use | CRITICAL VULNS |
|----------|-----------|-----|----------------|
| `strlen(s)` | `size_t strlen(char*)` | Length (excl `\0`) | Safe |
| `strcpy(dst, src)` | `char* strcpy(char*, const char*)` | **UNSAFE COPY** | Buffer overflow #1 |
| `strncpy(dst, src, n)` | `char* strncpy(char*, const char*, size_t)` | Copy ≤n | May omit `\0`! |
| `strcat(dst, src)` | `char* strcat(char*, const char*)` | Append | Overflow #2 |
| `strcmp(a,b)` | `int strcmp(const char*, const char*)` | Compare | `<0`,`0`,`>0` |
| `strchr(s, c)` | `char* strchr(const char*, int)` | Find char | Returns NULL if missing |
| `strstr(hay, needle)` | `char* strstr(const char*, const char*)` | Substring | NULL if not found |

**SAFE Alternatives** (BSD/Glibc):
```c
strlcpy(dst, src, sizeof(dst));  // Truncates safely
strlcat(dst, src, sizeof(dst));
```

## **3. stdlib.h - General Utilities**
| Function | Use | Vuln Notes |
|----------|-----|------------|
| `malloc(size)` | Heap alloc | `free()` required |
| `calloc(n, size)` | Zero-init alloc | Safer |
| `realloc(ptr, size)` | Resize heap | UAF if shrinks |
| `atoi(str)` | String → int | No error check |
| `atol(str)` | String → long | Overflow possible |
| `system(cmd)` | **Shell exec** | `system("/bin/sh")` RCE |
| `exit(n)` | Terminate | `exit(0)` success |
| `rand()` | Pseudo-random | Predictable |
| `srand(time(NULL))` | Seed random | Time-based attacks |

## **4. math.h - Mathematics**
```c
#include <math.h>
double x = 16.0;
sqrt(x);    // 4.0
pow(x, 0.5); // sqrt equiv
floor(x);   // 16.0
ceil(x);    // 16.0
sin(x);     // radians!
fabs(x);    // absolute value
round(x);   // nearest int
```

**Constants**: `M_PI`, `M_E`, `INFINITY`

## **5. stdbool.h & stdint.h - Modern Types**
```c
#include <stdbool.h>
bool flag = true;     // _Bool underhood

#include <stdint.h>
uint8_t  b;           // unsigned char
uint16_t w;           // unsigned short
uint32_t dw;          // unsigned int
uint64_t qw;          // unsigned long long
int64_t  i64;
```

## **6. ctype.h - Character Classification**
| Function | Checks | Example |
|----------|--------|---------|
| `isalpha(c)` | Letter | `'A'`-`'Z'`, `'a'`-`'z'` |
| `isdigit(c)` | Digit | `'0'`-`'9'` |
| `isalnum(c)` | Alpha+digit | Valid usernames |
| `isspace(c)` | Space/tab/NL | Tokenize input |
| `toupper(c)` / `tolower(c)` | Case convert | Case-insensitive strcmp |

```c
if(isalpha(user[0]))  // Valid username start
```

## **7. time.h - Timing & Random**
```c
#include <time.h>
time_t t = time(NULL);     // Epoch seconds
struct tm *tm = localtime(&t);
printf("%04d-%02d-%02d", tm->tm_year+1900, tm->tm_mon+1, tm->tm_mday);

srand(time(NULL));         // Seed
int r = rand() % 100;      // 0-99
```

## **8. File I/O Complete Pattern**
```c
FILE *f = fopen("data.bin", "wb");  // Write binary
if(!f) return 1;

struct User u = {"alice", 25};
fwrite(&u, sizeof(u), 1, f);  // Binary dump
fclose(f);

f = fopen("data.bin", "rb");
fread(&u, sizeof(u), 1, f);   // Read back
```

## **9. Beginner Lab: Common Patterns**
```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

int main() {
    char buf[64];
    char name[32];
    
    // DANGEROUS
    gets(buf);              // NEVER!
    strcpy(buf, "AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA");
    
    // SAFE
    fgets(name, sizeof(name), stdin);
    printf("Hello, %s", name);  // Still format risk!
    
    int *p = malloc(10 * sizeof(int));
    free(p);
    return 0;
}
```

## **10. Pentest Danger Ranking (Memorize)**
```
1. gets()           → Instant stack overflow
2. strcpy/strcat    → Buffer overflow
3. scanf("%s", buf) → No bounds
4. printf(user)     → Format string
5. system(user)     → Command injection
6. atoi(user)       → Integer overflow
```

**Golden Rule**: **Always pass `sizeof(buffer)`** to copy functions.

**Compile Check**: `gcc -Wall -Wextra -std=c99 -D_FORTIFY_SOURCE=2`
