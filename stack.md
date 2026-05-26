# stack

---

#  Introduction

In embedded systems, the **stack** is a critical memory region used for:

* function calls
* local variables
* return addresses
* interrupt context saving

It is one of the fastest and most automatically managed memory areas in a microcontroller.

---

#  Definition

The stack is a **LIFO (Last In First Out)** memory structure used to manage function execution and temporary data in a program.

---

#  Stack Characteristics

* Automatically managed by compiler
* Stores local variables
* Stores function call information
* Very fast access
* Limited size
* Grows and shrinks automatically

---

#  Stack Working Flow

```text id="flow1"
Function called
      |
      v
Stack frame created
      |
      v
Local variables stored
      |
      v
Function executes
      |
      v
Function returns
      |
      v
Stack frame removed
```

---

#  Stack Memory Diagram

```text id="diag1"
Higher Address
+-------------------+
| Return Address    |
| Parameters        |
| Local Variables   |
+-------------------+  ← Stack Pointer (SP)
|                   |
|   Stack grows ↓   |
|                   |
+-------------------+
Lower Address
```

---

#  Simple Example

```c id="ex1"
#include <stdio.h>

void func() {
    int a = 10;   // stored in stack
    int b = 20;   // stored in stack
    printf("%d %d\n", a, b);
}

int main() {
    func();
    return 0;
}
```

---

#  Stack Flow for Above Code

```text id="flow2"
main() called
   |
   v
Stack frame (main)
   |
   v
func() called
   |
   v
Stack frame (func) created
   |
   v
func() ends → frame removed
   |
   v
back to main()
```

---

#  Embedded System Importance

Stack is used in:

* interrupt handling
* RTOS task switching
* function recursion
* local buffer storage
* driver execution flow

---

#  Real-Time Embedded Example

## Interrupt + Stack Usage

```c id="ex2"
void ISR_Button() {
    int temp = 5; // stored in stack
}
```

---

#  ISR Stack Flow

```text id="flow3"
Interrupt occurs
      |
      v
CPU saves context on stack
      |
      v
ISR executes (uses stack)
      |
      v
Context restored from stack
      |
      v
Program resumes
```

---

#  Stack vs Heap

| Feature    | Stack          | Heap        |
| ---------- | -------------- | ----------- |
| Speed      | Fast           | Slow        |
| Management | Automatic      | Manual      |
| Size       | Limited        | Large       |
| Allocation | LIFO           | Random      |
| Risk       | Stack overflow | Memory leak |

---

#  Stack Growth in Microcontrollers

```text id="flow4"
High Memory Address
       ↓
   Stack grows downward
       ↓
   Heap grows upward
Low Memory Address
```

---

#  Stack Overflow

##  Definition

Occurs when stack memory exceeds its limit.

---

## Causes

* deep recursion
* large local arrays
* too many function calls
* ISR nesting

---

## Example

```c id="err1"
void recursive() {
    recursive(); //  infinite recursion
}
```

---

## Result

```text id="flow5"
Stack grows continuously
        |
        v
Overwrites other memory
        |
        v
System crash / reset
```

---

#  Large Stack Usage Example

```c id="err2"
void func() {
    int bigArray[10000]; // ❌ risky in embedded
}
```

---

#  Safe Stack Usage

```c id="safe1"
void func() {
    int smallArray[10]; // safe
}
```

---

#  Stack Frame Contents

Each function call creates a stack frame:

```text id="frame1"
+--------------------+
| Return Address     |
| Saved Registers    |
| Parameters         |
| Local Variables    |
+--------------------+
```

---

#  Stack in Interrupts

```text id="flow6"
Main program running
       |
       v
Interrupt occurs
       |
       v
CPU pushes registers to stack
       |
       v
ISR executes
       |
       v
Registers restored from stack
```

---

#  Embedded Real-Time Example

```c id="ex3"
void taskA() {
    int x = 10; // stack
}

void taskB() {
    int y = 20; // stack
}
```

---

#  Advantages of Stack

| Advantage   | Explanation                  |
| ----------- | ---------------------------- |
| Fast access | Very quick memory operations |
| Automatic   | No manual management         |
| Efficient   | Low overhead                 |
| Safe scope  | Variables auto-destroyed     |

---

#  Disadvantages

| Issue          | Explanation                   |
| -------------- | ----------------------------- |
| Limited size   | Small memory region           |
| Stack overflow | Can crash system              |
| Not persistent | Data lost after function ends |
| Not flexible   | Fixed structure               |

---

