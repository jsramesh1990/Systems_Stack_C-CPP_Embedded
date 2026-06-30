# Heap in C - Complete Guide

## Table of Contents

1. [Introduction](#1-introduction)
2. [Definition](#2-definition)
3. [Key Characteristics](#3-key-characteristics)
4. [Heap Working Flow](#4-heap-working-flow)
5. [Heap Memory Layout](#5-heap-memory-layout)
6. [Simple Example](#6-simple-example)
7. [Heap Flow Diagram](#7-heap-flow-diagram)
8. [Embedded System Use Cases](#8-embedded-system-use-cases)
9. [Real-Time Embedded Example - UART Buffer](#9-real-time-embedded-example---uart-buffer)
10. [Heap Fragmentation Problem](#10-heap-fragmentation-problem)
11. [Fragmentation Diagram](#11-fragmentation-diagram)
12. [Heap vs Stack](#12-heap-vs-stack)
13. [malloc / calloc / realloc](#13-malloc--calloc--realloc)
14. [Heap Growth Direction](#14-heap-growth-direction)
15. [Embedded Risk Example](#15-embedded-risk-example)
16. [Heap Usage Over Time](#16-heap-usage-over-time)
17. [Common Heap Issues](#17-common-heap-issues)
18. [Best Practices in Embedded Systems](#18-best-practices-in-embedded-systems)
19. [Heap in Embedded Systems](#19-heap-in-embedded-systems)
20. [Advantages](#20-advantages)
21. [Disadvantages](#21-disadvantages)
22. [Real-Time Examples](#22-real-time-examples)
23. [Best Practices](#23-best-practices)
24. [Quick Revision](#24-quick-revision)
25. [Interview Questions](#25-interview-questions)
26. [Conclusion](#26-conclusion)

---

## 1. Introduction

### What is Heap?

In embedded systems, the **heap** is a memory region used for **dynamic memory allocation** during program execution. Unlike stack memory, heap memory is manually managed by the programmer using functions like `malloc()`, `calloc()`, `realloc()`, and `free()` in C.

### Simple Definition

```
Heap = Dynamically allocated memory area
```

### Why Heap is Needed

```
✓ When data size is unknown at compile time
✓ For variable-sized data structures
✓ For communication buffers
✓ For dynamic data storage
```

### Visual Representation

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Heap Memory Region:                                   │
│  ┌─────────────────────────────────────────────────┐   │
│  │  ┌───────────────────────────────────────────┐ │   │
│  │  │  Block 1 (malloc(10))                    │ │   │
│  │  ├───────────────────────────────────────────┤ │   │
│  │  │  Block 2 (malloc(20))                    │ │   │
│  │  ├───────────────────────────────────────────┤ │   │
│  │  │  Block 3 (malloc(15))                    │ │   │
│  │  ├───────────────────────────────────────────┤ │   │
│  │  │  Free Memory                            │ │   │
│  │  └───────────────────────────────────────────┘ │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 2. Definition

### Formal Definition

The heap is a **dynamically allocated memory area** used when the size of data is not known at compile time.

### Simple Definition

```
Heap = Dynamic memory for runtime allocation
```

### Key Points

```
✓ Dynamic memory allocation
✓ Manual memory management
✓ Shared global memory pool
✓ Slower than stack
✓ Risk of fragmentation
✓ Located in RAM
```

### When to Use Heap

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Use Heap When:                                        │
│  ┌─────────────────────────────────────────────────┐   │
│  │  1. Size unknown at compile time               │   │
│  │  2. Need dynamic data structures               │   │
│  │  3. Variable-length arrays                     │   │
│  │  4. Large data that doesn't fit on stack      │   │
│  │  5. Shared data across functions              │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 3. Key Characteristics

### Heap Characteristics

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  1. Dynamic Memory Allocation                          │
│     └── Allocated at runtime                          │
│                                                         │
│  2. Manual Memory Management                           │
│     └── Programmer handles malloc/free                │
│                                                         │
│  3. Shared Global Memory Pool                          │
│     └── Accessible from anywhere in program           │
│                                                         │
│  4. Slower than Stack                                  │
│     └── Requires searching for free blocks            │
│                                                         │
│  5. Risk of Fragmentation                              │
│     └── Memory becomes scattered over time            │
│                                                         │
│  6. Located in RAM                                     │
│     └── Volatile memory                               │
│                                                         │
│  7. Larger than Stack                                  │
│     └── Can allocate large blocks                     │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### Heap Management Functions

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Heap Management Functions:                            │
│  ┌─────────────────────────────────────────────────┐   │
│  │  malloc()   → Allocate uninitialized memory   │   │
│  │  calloc()   → Allocate zero-initialized memory│   │
│  │  realloc()  → Resize existing memory block    │   │
│  │  free()     → Release memory block            │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 4. Heap Working Flow

### Complete Heap Allocation Flow

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Program requests memory (malloc)                      │
│       ↓                                                │
│  ┌─────────────────────────────────────────────────┐   │
│  │  Heap allocator searches free block            │   │
│  │  ┌───────────────────────────────────────────┐ │   │
│  │  │  1. Check size requirement                │ │   │
│  │  │  2. Find suitable free block             │ │   │
│  │  │  3. Split if block is too large          │ │   │
│  │  └───────────────────────────────────────────┘ │   │
│  └─────────────────────────────────────────────────┘   │
│       ↓                                                │
│  ┌─────────────────────────────────────────────────┐   │
│  │  Memory block assigned                         │   │
│  │  └── Mark block as used                       │   │
│  └─────────────────────────────────────────────────┘   │
│       ↓                                                │
│  ┌─────────────────────────────────────────────────┐   │
│  │  Pointer returned to program                   │   │
│  └─────────────────────────────────────────────────┘   │
│       ↓                                                │
│  ┌─────────────────────────────────────────────────┐   │
│  │  Program uses memory                           │   │
│  └─────────────────────────────────────────────────┘   │
│       ↓                                                │
│  ┌─────────────────────────────────────────────────┐   │
│  │  Memory freed (free)                           │   │
│  │  └── Mark block as free                       │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### Allocation Process Diagram

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  malloc(10):                                           │
│  ┌─────────────────────────────────────────────────┐   │
│  │  Before:                                      │   │
│  │  ┌──────────┬──────────┬──────────┬────────┐ │   │
│  │  │  Free    │  Free    │  Free    │  Free  │ │   │
│  │  │  20      │  30      │  15      │  25    │ │   │
│  │  └──────────┴──────────┴──────────┴────────┘ │   │
│  │                                                 │   │
│  │  After malloc(10):                             │   │
│  │  ┌──────────┬──────────┬──────────┬────────┐ │   │
│  │  │  Used    │  Free    │  Free    │  Free  │ │   │
│  │  │  10      │  10      │  15      │  25    │ │   │
│  │  └──────────┴──────────┴──────────┴────────┘ │   │
│  │             ↑                                   │   │
│  │          Pointer returns                       │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 5. Heap Memory Layout

### Complete Memory Layout with Heap

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│                    HEAP MEMORY LAYOUT                              │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                                                             │   │
│  │  HIGH ADDRESS                                              │   │
│  │  ┌───────────────────────────────────────────────────────┐ │   │
│  │  │  Stack (grows down)                                 │ │   │
│  │  │  ─────────────────────────────────────────────────  │ │   │
│  │  │  Function frames                                    │ │   │
│  │  │  Local variables                                    │ │   │
│  │  └───────────────────────────────────────────────────────┘ │   │
│  │                      ↓  (grows down)                       │   │
│  │  ┌───────────────────────────────────────────────────────┐ │   │
│  │  │  FREE MEMORY                                        │ │   │
│  │  │  ┌─────────────────────────────────────────────────┐ │ │   │
│  │  │  │  Available for stack and heap growth          │ │ │   │
│  │  │  └─────────────────────────────────────────────────┘ │ │   │
│  │  └───────────────────────────────────────────────────────┘ │   │
│  │                      ↑  (grows up)                        │   │
│  │  ┌───────────────────────────────────────────────────────┐ │   │
│  │  │  HEAP (grows up)                                    │ │   │
│  │  │  ┌─────────────────────────────────────────────────┐ │ │   │
│  │  │  │  malloc(20) → 20 bytes                        │ │ │   │
│  │  │  │  malloc(15) → 15 bytes                        │ │ │   │
│  │  │  │  malloc(10) → 10 bytes                        │ │ │   │
│  │  │  └─────────────────────────────────────────────────┘ │ │   │
│  │  └───────────────────────────────────────────────────────┘ │   │
│  │                                                             │   │
│  │  ┌───────────────────────────────────────────────────────┐ │   │
│  │  │  .bss Section                                       │ │   │
│  │  │  ─────────────────────────────────────────────────  │ │   │
│  │  │  Uninitialized global variables                     │ │   │
│  │  └───────────────────────────────────────────────────────┘ │   │
│  │                                                             │   │
│  │  ┌───────────────────────────────────────────────────────┐ │   │
│  │  │  .data Section                                      │ │   │
│  │  │  ─────────────────────────────────────────────────  │ │   │
│  │  │  Initialized global variables                       │ │   │
│  │  └───────────────────────────────────────────────────────┘ │   │
│  │                                                             │   │
│  │  LOW ADDRESS                                               │   │
│  │                                                             │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Heap Region Details

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Heap Region:                                          │
│  ┌─────────────────────────────────────────────────┐   │
│  │  Address    │  Content                        │   │
│  ├─────────────────────────────────────────────────┤   │
│  │  0x20000100 │  Heap Start Address              │   │
│  │  0x20000104 │  ┌─────────────────────────────┐ │   │
│  │  0x20000108 │  │  malloc(10) → 10 bytes    │ │   │
│  │  0x20000112 │  │  ptr1 = 0x20000104       │ │   │
│  │  0x20000116 │  ├─────────────────────────────┤ │   │
│  │  0x2000011A │  │  malloc(20) → 20 bytes    │ │   │
│  │  0x2000012E │  │  ptr2 = 0x20000118       │ │   │
│  │  0x20000132 │  ├─────────────────────────────┤ │   │
│  │  0x20000136 │  │  free(ptr1) → freed       │ │   │
│  │  0x2000013A │  ├─────────────────────────────┤ │   │
│  │  0x2000013E │  │  malloc(15) → 15 bytes    │ │   │
│  │  0x2000014D │  │  ptr3 = 0x20000140       │ │   │
│  │  0x20000151 │  └─────────────────────────────┘ │   │
│  │  0x20000155 │  Free Memory                     │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 6. Simple Example

### Code Example

```c
#include <stdlib.h>

int main() {
    // Allocate memory on heap
    int *ptr = (int*)malloc(sizeof(int));
    
    if(ptr != NULL) {
        // Use memory
        *ptr = 10;
        
        // Free memory
        free(ptr);
    }
    
    return 0;
}
```

### Heap Flow Diagram for Example

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  1. malloc(sizeof(int))                               │
│     └── Requests 4 bytes from heap                    │
│                                                         │
│  2. Heap allocator finds free block                    │
│     └── Allocates 4 bytes block                       │
│                                                         │
│  3. Returns pointer to allocated memory                │
│     └── ptr = 0x20000104                              │
│                                                         │
│  4. Program uses memory                                │
│     └── *ptr = 10                                     │
│                                                         │
│  5. free(ptr)                                         │
│     └── Releases memory back to heap                  │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### Memory Snapshot

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Before malloc:                                        │
│  ┌─────────────────────────────────────────────────┐   │
│  │  Heap: [FREE] [FREE] [FREE] [FREE]            │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  After malloc(4):                                      │
│  ┌─────────────────────────────────────────────────┐   │
│  │  Heap: [USED] [FREE] [FREE] [FREE]            │   │
│  │          ↑                                     │   │
│  │       ptr points here                          │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  After *ptr = 10:                                      │
│  ┌─────────────────────────────────────────────────┐   │
│  │  Heap: [10] [FREE] [FREE] [FREE]              │   │
│  │          ↑                                     │   │
│  │       ptr points here                          │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  After free(ptr):                                      │
│  ┌─────────────────────────────────────────────────┐   │
│  │  Heap: [FREE] [FREE] [FREE] [FREE]            │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 7. Heap Flow Diagram

### Complete Heap Operation Flow

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│                      HEAP OPERATION FLOW                           │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                                                             │   │
│  │  ┌─────────────────────────────────────────────────────┐   │   │
│  │  │  malloc(size)                                     │   │   │
│  │  │  ┌───────────────────────────────────────────────┐ │   │   │
│  │  │  │  1. Calculate required size (size + header) │ │   │   │
│  │  │  │  2. Search free list for suitable block    │ │   │   │
│  │  │  │  3. If found, mark as used                 │ │   │   │
│  │  │  │  4. Return pointer to user data            │ │   │   │
│  │  │  │  5. If not found, return NULL              │ │   │   │
│  │  │  └───────────────────────────────────────────────┘ │   │   │
│  │  └─────────────────────────────────────────────────────┘   │   │
│  │                                                             │   │
│  │  ┌─────────────────────────────────────────────────────┐   │   │
│  │  │  free(ptr)                                       │   │   │
│  │  │  ┌───────────────────────────────────────────────┐ │   │   │
│  │  │  │  1. Validate pointer                        │ │   │   │
│  │  │  │  2. Get block header from pointer           │ │   │   │
│  │  │  │  3. Mark block as free                      │ │   │   │
│  │  │  │  4. Merge with adjacent free blocks         │ │   │   │
│  │  │  │  5. Return to free list                     │ │   │   │
│  │  │  └───────────────────────────────────────────────┘ │   │   │
│  │  └─────────────────────────────────────────────────────┘   │   │
│  │                                                             │   │
│  │  ┌─────────────────────────────────────────────────────┐   │   │
│  │  │  realloc(ptr, new_size)                          │   │   │
│  │  │  ┌───────────────────────────────────────────────┐ │   │   │
│  │  │  │  1. If ptr is NULL, call malloc(new_size)  │ │   │   │
│  │  │  │  2. If new_size is 0, call free(ptr)       │ │   │   │
│  │  │  │  3. Try to resize in place                 │ │   │   │
│  │  │  │  4. If not possible, allocate new block    │ │   │   │
│  │  │  │  5. Copy data to new block                 │ │   │   │
│  │  │  │  6. Free old block                         │ │   │   │
│  │  │  │  7. Return new pointer                     │ │   │   │
│  │  │  └───────────────────────────────────────────────┘ │   │   │
│  │  └─────────────────────────────────────────────────────┘   │   │
│  │                                                             │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 8. Embedded System Use Cases

### Where Heap is Used in Embedded

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Heap is Used In:                                      │
│  ┌─────────────────────────────────────────────────┐   │
│  │  Dynamic Buffers                              │   │
│  │  └── Variable-size communication buffers      │   │
│  │                                                 │   │
│  │  Communication Stacks                         │   │
│  │  └── UART, SPI, I2C, CAN                     │   │
│  │                                                 │   │
│  │  Sensor Data Storage                          │   │
│  │  └── Logging, buffering                       │   │
│  │                                                 │   │
│  │  Image Processing                             │   │
│  │  └── Frame buffers, filters                   │   │
│  │                                                 │   │
│  │  RTOS Dynamic Tasks                          │   │
│  │  └── Limited use, often avoided               │   │
│  │                                                 │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### When to Avoid Heap in Embedded

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Avoid Heap When:                                      │
│  ┌─────────────────────────────────────────────────┐   │
│  │  ✗ Hard real-time constraints                  │   │
│  │  ✗ Memory is severely limited                  │   │
│  │  ✗ Safety-critical systems                     │   │
│  │  ✗ Deterministic timing required               │   │
│  │  ✗ Interrupt service routines                  │   │
│  │  ✗ After heap initialization                   │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 9. Real-Time Embedded Example - UART Buffer

### Dynamic UART Buffer Example

```c
#include <stdlib.h>
#include <stdint.h>

// Global pointer to UART buffer
char *uartBuffer = NULL;

// Initialize UART with dynamic buffer
void uart_init(uint32_t bufferSize) {
    // Allocate buffer on heap
    uartBuffer = (char*)malloc(bufferSize);
    
    if(uartBuffer != NULL) {
        // Buffer allocated successfully
        // Initialize UART hardware
    }
}

// Send data using buffer
void uart_send_data(char data) {
    if(uartBuffer != NULL) {
        uartBuffer[0] = data;
        // Send data via UART
    }
}

// Clean up
void uart_cleanup(void) {
    if(uartBuffer != NULL) {
        free(uartBuffer);
        uartBuffer = NULL;
    }
}

int main() {
    // Allocate 128-byte buffer
    uart_init(128);
    
    // Use UART
    uart_send_data('A');
    
    // Clean up
    uart_cleanup();
    
    return 0;
}
```

### Memory Flow Diagram

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  UART Buffer Allocation:                               │
│  ┌─────────────────────────────────────────────────┐   │
│  │  uart_init(128)                               │   │
│  │      │                                         │   │
│  │      ↓                                         │   │
│  │  malloc(128) allocates buffer                  │   │
│  │  ┌───────────────────────────────────────────┐ │   │
│  │  │  Heap: [UART Buffer 128 bytes]          │ │   │
│  │  │         ↑                               │ │   │
│  │  │    uartBuffer points here               │ │   │
│  │  └───────────────────────────────────────────┘ │   │
│  │                                                 │   │
│  │  uart_send_data('A')                          │   │
│  │      │                                         │   │
│  │      ↓                                         │   │
│  │  uartBuffer[0] = 'A'                         │   │
│  │  ┌───────────────────────────────────────────┐ │   │
│  │  │  Heap: [A] [ ] [ ] [ ] ... [ ]          │ │   │
│  │  └───────────────────────────────────────────┘ │   │
│  │                                                 │   │
│  │  uart_cleanup()                               │   │
│  │      │                                         │   │
│  │      ↓                                         │   │
│  │  free(uartBuffer) releases memory             │   │
│  │  ┌───────────────────────────────────────────┐ │   │
│  │  │  Heap: [FREE]                           │ │   │
│  │  └───────────────────────────────────────────┘ │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 10. Heap Fragmentation Problem

### What is Fragmentation?

Heap fragmentation occurs when repeated allocation and deallocation creates many small, non-contiguous free blocks that cannot be used for larger allocations.

### Fragmentation Process

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Initial Heap (contiguous):                            │
│  ┌─────────────────────────────────────────────────┐   │
│  │  [FREE] [FREE] [FREE] [FREE] [FREE] [FREE]  │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  After Allocations:                                    │
│  ┌─────────────────────────────────────────────────┐   │
│  │  [A:10] [B:20] [C:15] [D:25] [E:10] [F:20]  │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  After Free(B) and Free(E):                           │
│  ┌─────────────────────────────────────────────────┐   │
│  │  [A:10] [FREE] [C:15] [D:25] [FREE] [F:20]  │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  After More Operations:                                │
│  ┌─────────────────────────────────────────────────┐   │
│  │  [A:10] [FREE:5] [C:15] [FREE:8] [F:20]     │   │
│  │           (too small to use)   (too small)    │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  Large Allocation (30 bytes) Fails!                   │
│  ┌─────────────────────────────────────────────────┐   │
│  │  [A:10] [FREE:5] [C:15] [FREE:8] [F:20]     │   │
│  │           ✗ Cannot allocate 30 bytes!          │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### Fragmentation Types

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Types of Fragmentation:                               │
│  ┌─────────────────────────────────────────────────┐   │
│  │  1. Internal Fragmentation                     │   │
│  │     └── Wasted space within allocated blocks   │   │
│  │     └── Example: Allocated 100 bytes, using 60│   │
│  │                                                 │   │
│  │  2. External Fragmentation                     │   │
│  │     └── Wasted space between allocated blocks  │   │
│  │     └── Example: Many small free blocks       │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 11. Fragmentation Diagram

### Visual Fragmentation

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│                     FRAGMENTATION DIAGRAM                          │
│                                                                     │
│  Initial Heap:                                                     │
│  ┌─────────────────────────────────────────────────────────────────┐│
│  │ [FREE] [FREE] [FREE] [FREE] [FREE] [FREE] [FREE] [FREE]      ││
│  └─────────────────────────────────────────────────────────────────┘│
│                                                                     │
│  After Allocating (A, B, C, D, E):                                 │
│  ┌─────────────────────────────────────────────────────────────────┐│
│  │ [  A  ] [  B  ] [  C  ] [  D  ] [  E  ] [FREE] [FREE]      ││
│  │  10     20     15     25     10     10     20               ││
│  └─────────────────────────────────────────────────────────────────┘│
│                                                                     │
│  After Free(B) and Free(D):                                       │
│  ┌─────────────────────────────────────────────────────────────────┐│
│  │ [  A  ] [FREE] [  C  ] [FREE] [  E  ] [FREE] [FREE]      ││
│  │  10     20     15     25     10     10     20               ││
│  └─────────────────────────────────────────────────────────────────┘│
│                                                                     │
│  Fragmentation (small free blocks):                               │
│  ┌─────────────────────────────────────────────────────────────────┐│
│  │ [  A  ] [F:5] [  C  ] [F:8] [  E  ] [FREE] [FREE]      ││
│  │  10     5     15     8     10     10     20               ││
│  │         ↑                            ↑                      ││
│  │    Too small to use           Could use here                 ││
│  └─────────────────────────────────────────────────────────────────┘│
│                                                                     │
│  Result: Cannot allocate 30-byte block!                          │
│  ┌─────────────────────────────────────────────────────────────────┐│
│  │ [  A  ] [F:5] [  C  ] [F:8] [  E  ] [FREE] [FREE]      ││
│  │  10     5     15     8     10     10     20               ││
│  │                                                             ││
│  │  ✗ No contiguous 30-byte block available!                  ││
│  └─────────────────────────────────────────────────────────────────┘│
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Fragmentation Impact

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Fragmentation Impact:                                 │
│  ┌─────────────────────────────────────────────────┐   │
│  │  1. Memory Waste                              │   │
│  │     └── Free blocks unusable                  │   │
│  │                                                 │   │
│  │  2. Allocation Failures                        │   │
│  │     └── malloc() returns NULL                  │   │
│  │                                                 │   │
│  │  3. Unpredictable Behavior                     │   │
│  │     └── Different results each run             │   │
│  │                                                 │   │
│  │  4. Performance Degradation                    │   │
│  │     └── More searching for free blocks         │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 12. Heap vs Stack

### Comparison Table

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│                      HEAP vs STACK COMPARISON                      │
│                                                                     │
│  ┌────────────────────────────┬────────────────────────────────────┐│
│  │           STACK            │              HEAP                 ││
│  ├────────────────────────────┼────────────────────────────────────┤│
│  │  LIFO (Last In First Out)  │  Free Allocation                  ││
│  │  Automatic Management      │  Manual Management                ││
│  │  Very Fast Access          │  Slower Access                    ││
│  │  Fixed Size                │  Dynamic Size                     ││
│  │  No Fragmentation          │  Can Fragment                     ││
│  │  Limited Size (few KB)     │  Larger Size                      ││
│  │  Grows Downward            │  Grows Upward                     ││
│  │  Local Variables           │  malloc/free                      ││
│  │  Function Parameters       │  Dynamic Arrays                   ││
│  │  Return Addresses          │  Complex Data Structures          ││
│  │  Automatically Freed       │  Must be Manually Freed           ││
│  │  No Memory Leaks           │  Risk of Memory Leaks             ││
│  │  Preferred in RTOS         │  Avoided in RTOS                  ││
│  └────────────────────────────┴────────────────────────────────────┘│
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Visual Comparison

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│  STACK:                                                            │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  High Address │  Stack (grows down)                      │   │
│  │               │  ──────────────────────────────────────   │   │
│  │               │  Function 3 Frame                        │   │
│  │               │  Function 2 Frame                        │   │
│  │               │  Function 1 Frame                        │   │
│  │               │  main() Frame                            │   │
│  │  Low Address  │  ──────────────────────────────────────   │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  HEAP:                                                             │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  Low Address  │  ──────────────────────────────────────   │   │
│  │               │  malloc(10) → 10 bytes                   │   │
│  │               │  malloc(20) → 20 bytes                   │   │
│  │               │  malloc(15) → 15 bytes                   │   │
│  │  High Address │  ──────────────────────────────────────   │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### When to Use Each

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Use Stack:                                            │
│  ✓ Local variables                                    │
│  ✓ Function parameters                               │
│  ✓ Small, fixed-size data                            │
│  ✓ Temporary data                                    │
│  ✓ Fast, deterministic operations                    │
│                                                         │
│  Use Heap:                                             │
│  ✓ Unknown size at compile time                       │
│  ✓ Large data structures                              │
│  ✓ Data that needs to persist across functions        │
│  ✓ Dynamic arrays                                    │
│  ✓ When stack is too small                           │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 13. malloc / calloc / realloc

### malloc - Memory Allocation

```c
// Allocate uninitialized memory
int *p = (int*)malloc(10 * sizeof(int));

// Syntax: malloc(size_in_bytes)
// Returns: void* pointer to allocated memory
// Returns NULL if allocation fails
```

### calloc - Contiguous Allocation

```c
// Allocate and zero-initialize memory
int *p = (int*)calloc(10, sizeof(int));

// Syntax: calloc(count, size_per_element)
// All elements are initialized to 0
// Returns: void* pointer to allocated memory
// Returns NULL if allocation fails
```

### realloc - Reallocate Memory

```c
// Resize existing memory block
int *p = (int*)realloc(p, 20 * sizeof(int));

// Syntax: realloc(ptr, new_size)
// If ptr is NULL, works like malloc
// If new_size is 0, works like free
// Returns: void* pointer to new memory
// May move data to new location
```

### Comparison Table

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Function  │ Initialization  │ Parameters              │
│────────────┼─────────────────┼─────────────────────────│
│  malloc()  │  Uninitialized  │  size                   │
│  calloc()  │  Zero           │  count, size            │
│  realloc() │  Copies data    │  ptr, new_size          │
│  free()    │  Releases       │  ptr                    │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 14. Heap Growth Direction

### Heap and Stack Growth

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│               HEAP GROWTH DIRECTION IN MCU                         │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                                                             │   │
│  │  HIGH ADDRESS                                              │   │
│  │  ┌───────────────────────────────────────────────────────┐ │   │
│  │  │  ┌─────────────────────────────────────────────────┐  │ │   │
│  │  │  │  Stack (grows downward)                       │  │ │   │
│  │  │  │  ───────────────────────────────────────────── │  │ │   │
│  │  │  │  Function 3 Frame                            │  │ │   │
│  │  │  │  Function 2 Frame                            │  │ │   │
│  │  │  │  Function 1 Frame                            │  │ │   │
│  │  │  │  main() Frame                                │  │ │   │
│  │  │  └─────────────────────────────────────────────────┘  │ │   │
│  │  │                      ↓  (grows down)                  │ │   │
│  │  └───────────────────────────────────────────────────────┘ │   │
│  │                                                             │   │
│  │  ┌───────────────────────────────────────────────────────┐ │   │
│  │  │              FREE MEMORY                             │ │   │
│  │  │  ┌─────────────────────────────────────────────────┐  │ │   │
│  │  │  │  Available for stack and heap growth          │  │ │   │
│  │  │  └─────────────────────────────────────────────────┘  │ │   │
│  │  └───────────────────────────────────────────────────────┘ │   │
│  │                                                             │   │
│  │  ┌───────────────────────────────────────────────────────┐ │   │
│  │  │  ┌─────────────────────────────────────────────────┐  │ │   │
│  │  │  │  Heap (grows upward)                         │  │ │   │
│  │  │  │  ───────────────────────────────────────────── │  │ │   │
│  │  │  │  malloc(20) → 20 bytes                      │  │ │   │
│  │  │  │  malloc(15) → 15 bytes                      │  │ │   │
│  │  │  │  malloc(10) → 10 bytes                      │  │ │   │
│  │  │  └─────────────────────────────────────────────────┘  │ │   │
│  │  │                      ↑  (grows up)                    │ │   │
│  │  └───────────────────────────────────────────────────────┘ │   │
│  │                                                             │   │
│  │  LOW ADDRESS                                               │   │
│  │                                                             │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  Important: Heap grows upward, Stack grows downward               │
│  If they meet → System crash                                     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Heap Allocation Example

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Heap Allocation Sequence:                             │
│  ┌─────────────────────────────────────────────────┐   │
│  │  1. malloc(10) → allocates 10 bytes             │   │
│  │  2. malloc(20) → allocates 20 bytes             │   │
│  │  3. malloc(15) → allocates 15 bytes             │   │
│  │  4. free(ptr2) → releases 20 bytes             │   │
│  │  5. malloc(25) → allocates 25 bytes             │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  Heap grows upward with each allocation                │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 15. Embedded Risk Example

### Risky Code - Memory Leak

```c
#include <stdlib.h>

// ❌ RISKY - Memory leak in embedded system
int main() {
    while(1) {
        // Allocate memory but never free
        int *data = (int*)malloc(100 * sizeof(int));
        
        if(data != NULL) {
            // Use data
            data[0] = 10;
            data[1] = 20;
        }
        
        // No free → memory leak!
        // After many iterations → heap exhausted → crash
    }
    
    return 0;
}
```

### Memory Leak Diagram

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Memory Leak Over Time:                                │
│  ┌─────────────────────────────────────────────────┐   │
│  │  Iteration 1: [100 bytes allocated]            │   │
│  │  Iteration 2: [100 bytes] [100 bytes]          │   │
│  │  Iteration 3: [100] [100] [100]               │   │
│  │  Iteration 4: [100] [100] [100] [100]         │   │
│  │  Iteration 5: [100] [100] [100] [100] [100]   │   │
│  │  Iteration N: Heap Exhausted! ✗              │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  Result: System crash or undefined behavior           │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 16. Heap Usage Over Time

### Heap Usage Graph

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Heap Usage Over Time:                                 │
│                                                         │
│  RAM Usage ↑                                           │
│        │                                               │
│  100%  │              ✗ Crash!                        │
│        │            /                                  │
│   75%  │          /                                    │
│        │        /                                      │
│   50%  │      /                                        │
│        │    /                                          │
│   25%  │  /                                            │
│        │/                                              │
│    0%  └──────────────────────────────→ Time          │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │  1. Initial memory usage                       │   │
│  │  2. Allocations without free                  │   │
│  │  3. Heap usage increases                      │   │
│  │  4. Eventually, heap exhausted → crash       │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### Stable Heap Usage

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Stable Heap Usage (with proper free):                 │
│                                                         │
│  RAM Usage ↑                                           │
│        │                                               │
│   50%  │  ┌────┐  ┌────┐  ┌────┐  ┌────┐            │
│        │  │    │  │    │  │    │  │    │              │
│   25%  │  │    │  │    │  │    │  │    │              │
│        │  └────┘  └────┘  └────┘  └────┘              │
│    0%  └────────────────────────────────→ Time        │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │  ✓ Allocations balanced with frees             │   │
│  │  ✓ Heap usage stable                          │   │
│  │  ✓ No memory leaks                           │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 17. Common Heap Issues

### 1. Memory Leak

```c
// ❌ Memory leak
void leak_example() {
    int *ptr = (int*)malloc(sizeof(int));
    *ptr = 10;
    // No free → memory leak
}
```

### 2. Double Free

```c
// ❌ Double free
int *ptr = (int*)malloc(sizeof(int));
free(ptr);
free(ptr);  // Undefined behavior!
```

### 3. Dangling Pointer

```c
// ❌ Dangling pointer
int *ptr = (int*)malloc(sizeof(int));
free(ptr);
ptr = NULL;  // Good practice
```

### 4. Heap Overflow

```c
// ❌ Heap overflow
int *ptr = (int*)malloc(10 * sizeof(int));
ptr[10] = 100;  // Buffer overflow!
```

### Common Issues Summary

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Common Heap Issues:                                   │
│  ┌─────────────────────────────────────────────────┐   │
│  │  Issue          │  Cause           │  Solution │   │
│  ├─────────────────────────────────────────────────┤   │
│  │  Memory Leak    │  Missing free    │  Always   │   │
│  │                 │                  │  free     │   │
│  │                 │                  │           │   │
│  │  Double Free    │  Free twice      │  Set to   │   │
│  │                 │                  │  NULL     │   │
│  │                 │                  │           │   │
│  │  Dangling Ptr   │  Use after free  │  Set to   │   │
│  │                 │                  │  NULL     │   │
│  │                 │                  │           │   │
│  │  Heap Overflow  │  Buffer overflow │  Bounds   │   │
│  │                 │                  │  checks   │   │
│  │                 │                  │           │   │
│  │  Fragmentation  │  Many alloc/free │  Memory   │   │
│  │                 │                  │  pools    │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 18. Best Practices in Embedded Systems

### 1. Avoid Heap if Possible

```c
// Instead of malloc
int *ptr = malloc(size);

// Use static allocation
static int buffer[MAX_SIZE];
```

### 2. Always Free Memory

```c
int *ptr = malloc(size);
if(ptr != NULL) {
    // Use memory
    free(ptr);
    ptr = NULL;  // Good practice
}
```

### 3. Keep Allocations Small

```c
// ❌ Risky
int *big = malloc(10000);

// ✓ Better
static int big[MAX_SIZE];  // Or use heap only if necessary
```

### 4. Use Memory Pools

```c
// Pre-allocate memory pool
#define POOL_SIZE 1024
static uint8_t memory_pool[POOL_SIZE];

// Custom allocator using pool
void* pool_alloc(size_t size) {
    // Allocate from pool
}

void pool_free(void* ptr) {
    // Return to pool
}
```

### 5. Check Return Values

```c
int *ptr = malloc(size);
if(ptr == NULL) {
    // Handle allocation failure
    // Don't crash!
}
```

### 6. Set Pointers to NULL After Free

```c
free(ptr);
ptr = NULL;  // Prevents dangling pointer
```

---

## 19. Heap in Embedded Systems

### Heap Usage Guidelines

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Heap in Embedded Systems:                             │
│  ┌─────────────────────────────────────────────────┐   │
│  │  ✓ Restricted or disabled                      │   │
│  │  ✓ Carefully controlled                        │   │
│  │  ✓ Replaced with static buffers               │   │
│  │  ✓ Used only in non-real-time tasks           │   │
│  │  ✓ Used with memory pools                     │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  RTOS Heap Options:                                    │
│  ┌─────────────────────────────────────────────────┐   │
│  │  1. FreeRTOS: heap_n.c                        │   │
│  │  2. Static allocation preferred               │   │
│  │  3. Dynamic tasks often disabled              │   │
│  │  4. Memory pools for RTOS objects            │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### Why Heap is Often Disabled in RTOS

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Why Heap is Avoided in RTOS:                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │  1. Unpredictable Timing                      │   │
│  │     └── malloc() time varies                   │   │
│  │                                                 │   │
│  │  2. Fragmentation                            │   │
│  │     └── Memory becomes scattered               │   │
│  │                                                 │   │
│  │  3. Memory Leaks                            │   │
│  │     └── Hard to detect in RTOS                 │   │
│  │                                                 │   │
│  │  4. Determinism Required                       │   │
│  │     └── RTOS needs predictable timing          │   │
│  │                                                 │   │
│  │  5. Limited Memory                            │   │
│  │     └── MCUs have limited RAM                  │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 20. Advantages

### Heap Advantages

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│                      HEAP ADVANTAGES                               │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                                                             │   │
│  │  1. Dynamic Allocation                                     │   │
│  │     └── Allocate memory at runtime                         │   │
│  │     └── Size can be determined during execution             │   │
│  │                                                             │   │
│  │  2. Flexible Data Structures                              │   │
│  │     └── Create variable-sized structures                  │   │
│  │     └── Dynamic arrays, linked lists, trees               │   │
│  │                                                             │   │
│  │  3. Large Allocations                                     │   │
│  │     └── Can allocate large blocks                        │   │
│  │     └── Not limited by stack size                        │   │
│  │                                                             │   │
│  │  4. Persistent Data                                        │   │
│  │     └── Data persists across function calls               │   │
│  │     └── Shared between functions                          │   │
│  │                                                             │   │
│  │  5. Efficient for Variable Data                           │   │
│  │     └── Allocate only what's needed                       │   │
│  │     └── Reuse memory with realloc                         │   │
│  │                                                             │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 21. Disadvantages

### Heap Disadvantages

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│                     HEAP DISADVANTAGES                             │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                                                             │   │
│  │  1. Manual Management                                     │   │
│  │     └── Programmer must handle malloc/free                │   │
│  │     └── Risk of memory leaks                              │   │
│  │                                                             │   │
│  │  2. Fragmentation                                         │   │
│  │     └── Memory becomes scattered over time               │   │
│  │     └── Large allocations may fail                        │   │
│  │                                                             │   │
│  │  3. Slower Access                                         │   │
│  │     └── Need to search for free blocks                    │   │
│  │     └── More CPU cycles required                          │   │
│  │                                                             │   │
│  │  4. Unpredictable Timing                                  │   │
│  │     └── malloc/free time varies                           │   │
│  │     └── Not suitable for real-time                        │   │
│  │                                                             │   │
│  │  5. Memory Overhead                                       │   │
│  │     └── Extra memory for block headers                    │   │
│  │     └── Wasted memory                                     │   │
│  │                                                             │   │
│  │  6. Complexity                                            │   │
│  │     └── Harder to debug                                   │   │
│  │     └── More code to write                                │   │
│  │                                                             │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 22. Real-Time Examples

### Example 1: Dynamic String Buffer

```c
#include <stdlib.h>
#include <string.h>

char* create_string(const char* src) {
    size_t len = strlen(src);
    char* str = (char*)malloc(len + 1);
    
    if(str != NULL) {
        strcpy(str, src);
    }
    
    return str;
}

void process_string(const char* input) {
    char* buffer = create_string(input);
    if(buffer != NULL) {
        // Process buffer
        buffer[0] = toupper(buffer[0]);
        // ...
        free(buffer);
    }
}
```

### Example 2: Dynamic Array

```c
#include <stdlib.h>

typedef struct {
    int* data;
    size_t size;
    size_t capacity;
} DynamicArray;

DynamicArray* create_array(size_t initial_capacity) {
    DynamicArray* arr = (DynamicArray*)malloc(sizeof(DynamicArray));
    if(arr != NULL) {
        arr->data = (int*)malloc(initial_capacity * sizeof(int));
        arr->size = 0;
        arr->capacity = initial_capacity;
    }
    return arr;
}

void resize_array(DynamicArray* arr, size_t new_capacity) {
    int* new_data = (int*)realloc(arr->data, new_capacity * sizeof(int));
    if(new_data != NULL) {
        arr->data = new_data;
        arr->capacity = new_capacity;
    }
}

void free_array(DynamicArray* arr) {
    if(arr != NULL) {
        free(arr->data);
        free(arr);
    }
}
```

### Example 3: Linked List

```c
#include <stdlib.h>

typedef struct Node {
    int data;
    struct Node* next;
} Node;

Node* create_node(int data) {
    Node* node = (Node*)malloc(sizeof(Node));
    if(node != NULL) {
        node->data = data;
        node->next = NULL;
    }
    return node;
}

void append_node(Node** head, int data) {
    Node* new_node = create_node(data);
    if(new_node == NULL) return;
    
    if(*head == NULL) {
        *head = new_node;
        return;
    }
    
    Node* current = *head;
    while(current->next != NULL) {
        current = current->next;
    }
    current->next = new_node;
}

void free_list(Node* head) {
    while(head != NULL) {
        Node* temp = head;
        head = head->next;
        free(temp);
    }
}
```

### Example 4: Dynamic Buffer with Pool

```c
#include <stdlib.h>

// Memory pool
#define POOL_SIZE 1024
static uint8_t pool[POOL_SIZE];
static size_t pool_index = 0;

// Pool allocator
void* pool_alloc(size_t size) {
    if(pool_index + size <= POOL_SIZE) {
        void* ptr = &pool[pool_index];
        pool_index += size;
        return ptr;
    }
    return NULL;  // Out of pool memory
}

// Reset pool
void pool_reset(void) {
    pool_index = 0;
}

// Use pool instead of heap
void process_data(void) {
    int* buffer = (int*)pool_alloc(10 * sizeof(int));
    if(buffer != NULL) {
        buffer[0] = 10;
    }
}
```

---

## 23. Best Practices

### Heap Best Practices

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│                    HEAP BEST PRACTICES                             │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                                                             │   │
│  │  1. Always Check malloc Return Value                       │   │
│  │     └── if(ptr == NULL) handle error                      │   │
│  │                                                             │   │
│  │  2. Always Free Allocated Memory                         │   │
│  │     └── Every malloc needs a free                        │   │
│  │                                                             │   │
│  │  3. Set Pointers to NULL After Free                     │   │
│  │     └── free(ptr); ptr = NULL;                          │   │
│  │                                                             │   │
│  │  4. Don't Free Already Freed Memory                     │   │
│  │     └── Double free is undefined behavior               │   │
│  │                                                             │   │
│  │  5. Keep Allocations Small                              │   │
│  │     └── Large allocations cause fragmentation           │   │
│  │                                                             │   │
│  │  6. Use Memory Pools in RTOS                            │   │
│  │     └── Pre-allocate memory blocks                     │   │
│  │                                                             │   │
│  │  7. Avoid Heap in ISRs                                  │   │
│  │     └── malloc/free in ISR is dangerous                │   │
│  │                                                             │   │
│  │  8. Use Static Allocation When Possible                  │   │
│  │     └── Prefer static over dynamic                      │   │
│  │                                                             │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Embedded Heap Guidelines

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Embedded Heap Guidelines:                            │
│  ┌─────────────────────────────────────────────────┐   │
│  │  1. Limit heap size in linker script            │   │
│  │     └── Define max heap size                    │   │
│  │                                                 │   │
│  │  2. Use static buffers                         │   │
│  │     └── Avoid malloc when possible              │   │
│  │                                                 │   │
│  │  3. Use memory pools                           │   │
│  │     └── Pre-allocate fixed-size blocks         │   │
│  │                                                 │   │
│  │  4. Monitor heap usage                         │   │
│  │     └── Track allocation and deallocation      │   │
│  │                                                 │   │
│  │  5. Never use heap in interrupts              │   │
│  │     └── ISR should use static buffers          │   │
│  │                                                 │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 24. Quick Revision

### Heap Summary

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│                      HEAP QUICK REFERENCE                          │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                                                             │   │
│  │  Definition: Dynamically allocated memory area            │   │
│  │                                                             │   │
│  │  Functions:                                               │   │
│  │  malloc()   → Allocate uninitialized                     │   │
│  │  calloc()   → Allocate zero-initialized                  │   │
│  │  realloc()  → Resize memory                              │   │
│  │  free()     → Release memory                             │   │
│  │                                                             │   │
│  │  Characteristics:                                         │   │
│  │  ✓ Dynamic allocation                                    │   │
│  │  ✓ Manual management                                     │   │
│  │  ✓ Slower than stack                                     │   │
│  │  ✓ Risk of fragmentation                                 │   │
│  │  ✓ Risk of memory leaks                                  │   │
│  │                                                             │   │
│  │  Best Practices:                                          │   │
│  │  ✓ Check malloc return                                   │   │
│  │  ✓ Always free                                           │   │
│  │  ✓ Set pointers to NULL after free                      │   │
│  │  ✓ Avoid heap in RTOS                                   │   │
│  │  ✓ Use static buffers when possible                     │   │
│  │                                                             │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Common Heap Operations

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  Common Heap Operations:                               │
│  ┌─────────────────────────────────────────────────┐   │
│  │  Operation │ Code Example                      │   │
│  ├─────────────────────────────────────────────────┤   │
│  │  Allocate  │ int *p = malloc(10 * sizeof(int));│   │
│  │  Zero-init │ int *p = calloc(10, sizeof(int));│   │
│  │  Resize    │ p = realloc(p, 20 * sizeof(int));│   │
│  │  Free      │ free(p); p = NULL;                │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 25. Interview Questions

### Basic Questions

#### Q1: What is heap in C?
**Answer:** Heap is a memory region used for dynamic memory allocation at runtime.

#### Q2: What functions manage heap?
**Answer:** malloc(), calloc(), realloc(), free().

#### Q3: Difference between malloc and calloc?
**Answer:** malloc leaves memory uninitialized; calloc initializes to zero.

#### Q4: What is memory leak?
**Answer:** Allocated memory that is not freed, causing memory waste.

### Intermediate Questions

#### Q5: What is heap fragmentation?
**Answer:** Memory becomes divided into small non-contiguous free blocks.

#### Q6: Why avoid heap in embedded systems?
**Answer:** Fragmentation, unpredictable timing, memory leaks, limited resources.

#### Q7: What happens if malloc returns NULL?
**Answer:** Memory allocation failed - handle gracefully.

#### Q8: What is double free?
**Answer:** Freeing already freed memory - undefined behavior.

### Advanced Questions

#### Q9: How to prevent memory leaks?
**Answer:** Always free allocated memory, use static analysis tools.

#### Q10: What are memory pools?
**Answer:** Pre-allocated fixed-size memory blocks for efficient allocation.

#### Q11: Why is heap discouraged in RTOS?
**Answer:** Unpredictable timing, fragmentation, not deterministic.

#### Q12: How to debug heap issues?
**Answer:** Use valgrind, static analysis, memory tracking tools.

---

## 26. Conclusion

### Summary

Heap provides dynamic memory allocation capability in C, essential for variable-sized data structures. However, in embedded systems, heap usage requires careful management due to fragmentation, leaks, and performance concerns.

### Key Takeaways

```
✓ Heap = Dynamic memory allocation
✓ Managed by malloc/free
✓ Slower than stack
✓ Risk of fragmentation
✓ Risk of memory leaks
✓ Avoid in real-time systems
✓ Use static allocation when possible
```

### Best Practices Summary

```
✓ Always check malloc return
✓ Always free allocated memory
✓ Set pointers to NULL after free
✓ Avoid heap in interrupts
✓ Use memory pools in RTOS
✓ Prefer static allocation
```

### Learning Path

```
C Programming
    ↓
Memory Concepts
    ↓
Stack
    ↓
Heap → You are here
    ↓
Memory Layout
    ↓
RTOS
    ↓
Embedded Systems
```

### Final Thought

Heap is a powerful but risky tool in embedded systems. Use it sparingly and carefully. When possible, use static allocation or memory pools to avoid the pitfalls of heap memory management.

---

*End of Document*
