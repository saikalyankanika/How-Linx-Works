# Hardware: Understanding Main Memory

## Main Memory (RAM)

Main memory (RAM) is a large storage area that holds data in the form of bits.

- A **bit** is the smallest unit of data and can be either `0` or `1`.
- The Linux kernel, running processes, and application data all reside in RAM while executing.
- All input/output operations pass through memory before being processed by the CPU.

## Role of the CPU

The CPU operates on data stored in memory by continuously:

1. Reading instructions from memory.
2. Reading data from memory.
3. Processing the data.
4. Writing results back to memory.

In simple terms:

```text
Memory → CPU → Memory
```

# 1.3 The Kernel

## Overview

The kernel is the core component of the operating system and is responsible for managing system resources. Almost everything the kernel does revolves around memory management and maintaining system state.

The kernel manages four major areas:

1. Processes
2. Memory
3. Device Drivers
4. System Calls and Kernel Support

---

## 1.3.1 Process Management

Process management involves:

- Starting processes
- Scheduling processes
- Pausing processes
- Resuming processes
- Terminating processes

The kernel determines which process gets access to the CPU.

### Multitasking

Although many applications appear to run simultaneously, on a single-core CPU only one process can execute at any given moment.

Example:

```text
Time 1 → Process A
Time 2 → Process B
Time 3 → Process C
Time 4 → Process A
```

This rapid switching creates the illusion of simultaneous execution, known as **multitasking**.

### Time Slice

A **time slice** is the small amount of CPU time allocated to a process before the kernel switches to another process.

```text
| Process A | Process B | Process C | Process A |
```

### Context Switch

A **context switch** occurs when the CPU stops executing one process and starts executing another.

#### Context Switch Steps

1. CPU timer interrupts the current process.
2. CPU switches from User Mode to Kernel Mode.
3. Kernel saves the current CPU and memory state of the process.
4. Kernel handles pending tasks (I/O, interrupts, etc.).
5. Kernel selects the next process to run.
6. Kernel prepares memory and CPU state for the selected process.
7. CPU switches back to User Mode and execution continues.

```text
Process A
    ↓
Kernel
    ↓
Process B
```

### Important Point

The kernel generally runs during a context switch, between two process executions.

### Multi-Core Systems

On systems with multiple CPU cores:

```text
Core 1 → Process A
Core 2 → Process B
Core 3 → Process C
Core 4 → Process D
```

Multiple processes can run truly in parallel, but the kernel still manages scheduling and context switching.

---

## 1.3.2 Memory Management

The kernel is responsible for managing all memory in the system.

### Memory Management Requirements

The following conditions must be maintained:

- The kernel must have protected memory inaccessible to user processes.
- Each process must have its own memory space.
- Processes cannot access another process's private memory.
- Processes may share memory when required.
- Some memory regions can be read-only.
- The system can use more memory than physically available through disk-based storage.

### Memory Management Unit (MMU)

Modern CPUs contain a **Memory Management Unit (MMU)**.

The MMU enables **Virtual Memory**.

### Virtual Memory

Processes do not access physical memory directly.

Instead:

```text
Process
    ↓
Virtual Address
    ↓
MMU
    ↓
Physical Address
```

The MMU translates virtual memory addresses into physical memory locations.

### Benefits of Virtual Memory

- Process isolation
- Security
- Memory protection
- Simplified programming model

Each process behaves as if it owns the entire machine's memory.

### Page Table

The address translation map used by the MMU is called a **Page Table**.

Example:

```text
Virtual Address   → Physical Address

0x1000            → 0xA3000
0x2000            → 0xB7000
```

During a context switch, the kernel switches from one process's page table to another.

---

## 1.3.3 Device Drivers and Device Management

Most hardware devices can only be accessed through the kernel.

Examples:

- Disk Drives
- Network Cards
- Keyboards
- GPUs
- USB Devices

Direct hardware access by user processes could crash or compromise the system.

### Device Drivers

A **device driver** is software that allows the kernel to communicate with specific hardware.

```text
Application
      ↓
Kernel
      ↓
Device Driver
      ↓
Hardware
```

### Purpose of Device Drivers

Different hardware devices expose different interfaces.

The driver hides these differences and provides a consistent interface to applications.

Benefits:

- Hardware abstraction
- Simpler software development
- Device independence

---

## 1.3.4 System Calls and Kernel Support

### What is a System Call?

A **System Call (syscall)** is the mechanism through which a user process requests services from the kernel.

```text
User Process
      ↓
System Call
      ↓
Kernel
```

### Common System Calls

File Operations:

```text
open()
read()
write()
close()
```

These operations require kernel involvement.

---

### fork()

Creates a nearly identical copy of the current process.

```text
Process A
     ↓
fork()
     ↓
Parent + Child
```

---

### exec()

Replaces the current process with a new program.

```text
exec(program)
```

The existing process image is replaced by the specified program.

---

## How Linux Starts New Processes

Almost every user process is created using:

```text
fork()
```

followed by:

```text
exec()
```

### Example: Running `ls`

When a user runs:

```bash
ls
```

The shell performs:

```text
Shell
   ↓
fork()
   ↓
Shell Copy
   ↓
exec(ls)
   ↓
ls
```

### Process Creation Flow

```text
shell
   ↓
fork()
   ↓
copy of shell
   ↓
exec(ls)
   ↓
ls
```

### Note

System calls are usually written with parentheses:

```text
fork()
exec()
read()
write()
```

This notation originates from the C programming language.

---

## Pseudodevices

A **Pseudodevice** appears as a device file but is implemented entirely in software.

Examples:

```text
/dev/random
/dev/null
/dev/zero
```

### Why Pseudodevices Exist

They provide useful functionality without requiring physical hardware.

Example:

```text
/dev/random
```

Provides random data generated by the kernel.

### Important Note

Even when using a pseudodevice, processes still interact with it through system calls such as:

```text
open()
read()
write()
```

---

# Key Takeaways

- The kernel manages processes, memory, devices, and system calls.
- Process scheduling is performed through time slices and context switches.
- Context switches save one process state and load another.
- The MMU enables virtual memory by translating virtual addresses to physical addresses.
- Page tables maintain virtual-to-physical memory mappings.
- Device drivers allow the kernel to communicate with hardware.
- System calls provide a controlled interface between user processes and the kernel.
- Most Linux processes are created using `fork()` and start new programs using `exec()`.
- Pseudodevices behave like hardware devices but are implemented in software.

# Mental Model

```text
                    Linux Kernel
                           │
       ┌───────────────────┼───────────────────┐
       │                   │                   │
       ▼                   ▼                   ▼

   Processes          Memory            Devices

 Scheduling       Virtual Memory      Drivers
 Context Switch   Page Tables         Hardware Access

       │
       ▼

  System Calls

 fork()
 exec()
 read()
 write()

       │
       ▼

 User Applications
```