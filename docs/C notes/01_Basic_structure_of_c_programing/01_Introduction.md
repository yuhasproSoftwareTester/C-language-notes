
#  Introduction

## Overview
C is a **procedural, low-level, general-purpose language** created by Dennis Ritchie in 1971 at Bell Labs. It's the foundation for modern languages (C++, Java, Python) and excels in system programming, embedded systems, OS kernels (e.g., Linux), and performance-critical apps.

- **Key Strengths**: High efficiency, portability, direct hardware control.
- **Philosophy**: "Trust the programmer" – no automatic bounds checking, enabling power but risking vulnerabilities like buffer overflows.
- **Relevance**: Essential for exploit dev, reverse engineering, and understanding memory corruption attacks.

## Why Learn C?
- Compiles to native machine code for maximum speed.
- Runs on any platform with a compiler (e.g., GCC).
- Powers 90%+ of embedded devices and servers.
- Critical for security: Analyze CVEs involving pointers, stacks, heaps.

## Basic Program Structure
```c
#include <stdio.h>

int main() {
    printf("Hello, World!\n");
    return 0;
}
```

- `#include <stdio.h>`: Standard I/O library.
- `main()`: Program entry point (returns exit status: 0 = success).
- `printf()`: Formatted output.

## Compile & Run
```bash
gcc hello.c -o hello  # Compile
./hello               # Execute
```

**Output**: `Hello, World!`

## Core Philosophy
C gives **manual control** over memory (via pointers), I/O, and execution—perfect for optimization but demands precision to avoid crashes or exploits. 
