# heap

---

#  Introduction

In embedded systems, the **heap** is a memory region used for **dynamic memory allocation** during program execution.

Unlike stack memory, heap memory is manually managed by the programmer using functions like `malloc()`, `calloc()`, `realloc()`, and `free()` in C.

---

#  Definition

The heap is a **dynamically allocated memory area** used when the size of data is not known at compile time.

---

#  Key Characteristics

* Dynamic memory allocation
* Manual memory management
* Shared global memory pool
* Slower than stack
* Risk of fragmentation
* Located in RAM

---

#  Heap Working Flow

```text id="flow1"
Program requests memory (malloc)
            |
            v
Heap allocator searches free block
            |
            v
Memory block assigned
            |
            v
Pointer returned to program
            |
            v
Program uses memory
            |
            v
Memory freed (free)
```

---

#  Heap Memory Layout

```text id="diag1"
+----------------------+
| Stack (top)          |
|----------------------|
| Heap (grows ↑)       |
|----------------------|
| .bss                 |
| .data                |
| .text (Flash)        |
+----------------------+
```

---

#  Simple Example

```c id="ex1"
#include <stdlib.h>

int main() {
    int *ptr = (int*)malloc(sizeof(int));

    *ptr = 10;

    free(ptr);

    return 0;
}
```

---

#  Heap Flow for Above Code

```text id="flow2"
malloc request
      |
      v
Heap allocates memory
      |
      v
Pointer returned
      |
      v
Data stored
      |
      v
free() releases memory
```

---

#  Embedded System Use Cases

Heap is used in:

* dynamic buffers
* communication stacks (UART, SPI)
* sensor data storage
* image processing
* RTOS dynamic tasks (limited use)

---

#  Real-Time Embedded Example

## UART Buffer Allocation

```c id="ex2"
char *uartBuffer;

void init() {
    uartBuffer = (char*)malloc(128);
}

void send() {
    uartBuffer[0] = 'A';
}

void cleanup() {
    free(uartBuffer);
}
```

---

#  Heap Fragmentation Problem

```text id="flow3"
Allocate → Free → Allocate → Free
        |
        v
Memory becomes scattered blocks
        |
        v
Large block allocation fails
```

---

#  Fragmentation Diagram

```text id="frag1"
Before:
[####][####][####]

After:
[##][  ][###][ ]
```

---

#  Heap vs Stack

| Feature    | Heap        | Stack          |
| ---------- | ----------- | -------------- |
| Allocation | Dynamic     | Automatic      |
| Speed      | Slow        | Fast           |
| Management | Manual      | Automatic      |
| Risk       | Memory leak | Stack overflow |
| Size       | Large       | Limited        |

---

#  malloc / calloc / realloc

---

## malloc

```c id="h1"
int *p = (int*)malloc(10 * sizeof(int));
```

* uninitialized memory

---

## calloc

```c id="h2"
int *p = (int*)calloc(10, sizeof(int));
```

* initialized to zero

---

## realloc

```c id="h3"
p = (int*)realloc(p, 20 * sizeof(int));
```

* resize memory block

---

#  Heap Growth Direction

```text id="flow4"
Higher memory addresses
        ↑
        | Heap grows upward
        |
        ↓ Stack grows downward
Lower memory addresses
```

---

#  Embedded Risk Example

```c id="ex3"
while(1) {
    int *data = (int*)malloc(100);

    //  no free → memory leak
}
```

---

#  Heap Usage Over Time

```text id="graph1"
RAM usage ↑

|        /
|       /
|      /
|     /
|____/__________
   stable → leak → crash
```

---

#  Common Heap Issues

---

## 1. Memory Leak

```c id="m1"
ptr = malloc(100);
// no free → leak
```

---

## 2. Double Free

```c id="m2"
free(ptr);
free(ptr); //  undefined behavior
```

---

## 3. Dangling Pointer

```c id="m3"
free(ptr);
ptr = NULL;
```

---

## 4. Heap Overflow

```c id="m4"
malloc too much memory → system crash
```

---

#  Best Practices in Embedded Systems

---

## 1. Avoid heap if possible

```text id="safe1"
Use static memory instead of malloc
```

---

## 2. Always free memory

```c id="safe2"
free(ptr);
ptr = NULL;
```

---

## 3. Keep allocations small

```c id="safe3"
Avoid large dynamic buffers
```

---

## 4. Use memory pools (preferred in RTOS)

---

#  Heap in Embedded Systems

Heap is often:

* restricted or disabled
* carefully controlled
* replaced with static buffers
* used only in non-real-time tasks

---


