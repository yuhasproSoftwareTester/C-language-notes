# C Strings & Functions Summary

## **Strings in C**
```python
Strings are **null-terminated char arrays** (`\0` sentinel). No built-in type—manual management.
```

| Aspect | Syntax/Behavior | Key Notes |
|--------|-----------------|-----------|
| **Declaration** | `char str[64] = "hello";` <br> `char *ptr = "world";` | Array: modifiable <br> Literal: read-only (RODATA) |
| **Length** | `strlen(str)` <br> `sizeof(str)` | Excludes `\0` <br> Includes `\0` (fixed-size only) |
| **Copy** | `strcpy(dst, src)` <br> `strncpy(dst, src, n)` | Unsafe (no bounds) <br> Truncates, may omit `\0` |
| **Concat** | `strcat(dst, src)` <br> `strncat(dst, src, n)` | Unsafe (overflow) <br> Appends up to `n-1`, adds `\0` |
| **Compare** | `strcmp(a,b)` <br> `strncmp(a,b,n)` | Lexicographic (`<0`,`0`,`>0`) <br> First `n` chars |
| **Search** | `strchr(str, c)` <br> `strstr(hay, needle)` | First occurrence <br> Substring (NULL if missing) |

```c
char buf[16];
strcpy(buf, "AAAAAAAAAAAAAAAAAAAA");  // BOOM: stack overflow
```

**Pentest Targets**:
- `gets()` / `strcpy()` → buffer overflows (classic ROP chains)
- `strcat()` loops → heap overflows
- Format string exploits: `printf(user_input)`

## **String Functions Deep Dive**
| Function | Prototype | Vulns Exploited |
|----------|-----------|-----------------|
| `gets(buf)` | `char* gets(char*)` | **No bounds**—unlimited input → stack smash |
| `scanf("%s", buf)` | `int scanf(const char*, ...)` | No bounds on `%s` → overflow |
| `strcpy/strcat` | `char* strcpy(char*, const char*)` | No length check → copy past end |
| `sprintf(buf, fmt, ...)` | `int sprintf(char*, const char*, ...)` | Format string attacks (`%n`, `%x` leaks) |
| `atoi/atol` | `int atoi(const char*)` | Integer overflows, no validation |

**Safe Alternatives** (rarely used in legacy/vuln code):
```c
strlcpy(dst, src, sizeof(dst));  // BSD: truncates safely
snprintf(buf, sizeof(buf), fmt, args);  // Bounds-checked
fgets(buf, sizeof(buf), stdin);  // Line-safe input
```


## **Pentest Hotspots**
| Vuln Class         | Trigger                             | Payload Example                      |
| ------------------ | ----------------------------------- | ------------------------------------ |
| **Stack Overflow** | `strcpy(buf, long_input)`           | `"A"*80 + shellcode + ret2libc`      |
| **Format String**  | `printf(user_input)`                | `"%08x."*10 + "%n"`                  |
| **Heap Overflow**  | `strcat(heap_buf, long_str)`        | Partial overwrite → tcache poisoning |
| **Use-After-Free** | `free(str); strcpy(str, input);`    | Double-free → arbitrary alloc        |
| **Off-by-One**     | `strncpy(buf, src, 10); buf[10]=0;` | Null byte overwrite → info leak      |

**Classic Exploit** (stack overflow):
```c
// Vulnerable
void auth(char *user) {
    char pass[32];
    gets(pass);
    if (!strcmp(user, "admin") && !strcmp(pass, "secret"))
        system("/bin/sh");
}
// Payload: python -c 'print("A"*48 + "\xef\xbe\xad\xde")' | ./prog
```

**Next chat ready**: Ask for "pointers", "memory allocation", "file I/O", or specific exploits like "format string ROP chain"!