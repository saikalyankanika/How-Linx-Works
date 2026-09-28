# Abstraction
- Abstraction is the concept of **focusing on what** something does rather than **how it does it**. In other words, we are more concerned with the output or result of an action than with the internal processes that produce that result.

- In software systems, especially operating systems, many complex mechanisms are hidden or abstracted from users. As end users, we interact with the services and outputs provided by the OS without needing to understand all the internal details and implementations.

- This is not necessarily a bad thing. In fact, abstraction exists to simplify usage and reduce complexity. In many situations, there is no need to understand the underlying implementation unless we are developers, system administrators, or simply curious about how the system works.


# Linux System Architecture - High Level Overview

Linux can be viewed at a high level as a **3-layer system**:

```text
+-------------------------------+
| User Space                    |
| GUI, Shell, Servers, Apps     |
+-------------------------------+
| Kernel Space                  |
| System Calls                  |
| Process Management            |
| Memory Management             |
| Device Drivers                |
+-------------------------------+
| Hardware                      |
| CPU, RAM, Disks, NICs         |
+-------------------------------+
```


## 1. User Space (Top Layer)

This is where **user processes** run.

### Examples
- Shell (bash)
- GUI applications
- Web servers
- Databases
- Browsers
- Any application process

### Characteristics
- Runs in **User Mode**.
- Has access only to a restricted portion of memory.
- Cannot directly access hardware.
- Must request services from the kernel.

### Benefits
If one process crashes, the damage is usually isolated.

Example:

```text
process(ex - edge) crashes
      ↓
Kernel cleans up
      ↓
Other processes continue running
```

A faulty application typically does not bring down the entire operating system.

---

## 2. Kernel Space (Middle Layer)

The **Kernel** is the core component of the operating system.

Its primary role is to act as a **mediator between user applications and the hardware**.

### Responsibilities
- Process Scheduling
- Memory Management
- Device Management
- Filesystem Management
- Network Management
- Security and Permissions

Applications cannot access hardware directly. Instead, they communicate with the kernel using **System Calls**.

Example:

```text
Application
      ↓
  System Call
      ↓
    Kernel
      ↓
     Disk
```

### Kernel Mode

The kernel runs in **Kernel Mode**, which provides unrestricted access to:

- CPU
- RAM
- Hardware Devices

The memory region accessible only by the kernel is called **Kernel Space**.

---

## 3. Hardware Layer (Bottom Layer)

This layer contains the physical components of the system.

### Examples
- CPU(s)
- RAM
- Hard Disks / SSDs
- Network Interface Cards (NICs)
- GPUs
- Other Peripheral Devices

The hardware itself does not manage applications or allocate resources. The kernel is responsible for controlling and coordinating all hardware resources.

---

## Why This Separation Exists

Without this separation, applications could directly access hardware:

```text
Application
      ↓
 Direct Hardware Access
```

A bug in an application could:

- Corrupt memory
- Crash the entire machine
- Overwrite critical system data
- Affect all running processes

Instead, Linux enforces:

```text
Application
      ↓
    Kernel
      ↓
   Hardware
```

This architecture provides:

- Stability
- Security
- Process Isolation
- Resource Management

---

## Important Clarification: Kernel Mode vs Root User

These two concepts are often confused.

### Kernel Mode
- A CPU privilege level.
- Has unrestricted access to hardware and memory.
- Used only by the kernel and kernel threads.

### Root User
- An administrative Linux account.
- Has elevated permissions within the operating system.
- Still runs applications in **User Mode**.

Even a root-owned process cannot directly access hardware. It must still interact with the kernel through system calls.

---

## Short Notes

Linux can be thought of as a **3-layer architecture** consisting of:

### User Space
Contains user processes such as:
- GUI applications
- Shells
- Web servers
- Databases
- Browsers

These processes run in **User Mode** and have access only to a restricted portion of memory. They cannot directly interact with hardware and must communicate through the kernel.

### Kernel Space
The kernel is the core of the operating system. It acts as a mediator between applications and hardware, handling:

- Process Management
- Memory Management
- Device Management
- Networking
- Filesystems
- Security

The kernel runs in **Kernel Mode** and has full access to system resources. The memory exclusively accessible by the kernel is known as **Kernel Space**.

### Hardware Layer
The hardware layer consists of:

- CPUs
- RAM
- Disks
- Network Interfaces / Network Ports

The kernel manages and coordinates these resources so that applications can safely use them.

### Key Takeaway

The separation between User Space and Kernel Space exists to improve:

- Stability
- Security
- Fault Isolation

If a user process crashes, the kernel can usually clean it up without affecting the rest of the system. This prevents a single application failure from becoming a full system failure.

In short:

> User processes do the work, the kernel manages the work, and the hardware provides the resources needed to perform the work.