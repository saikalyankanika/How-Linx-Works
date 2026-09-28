# 1.4 User Space

## What is User Space?

**User Space** is the portion of main memory allocated by the kernel for user processes.

Since a process is simply a state (or image) in memory, user space can also refer to the memory occupied by all running user processes.

### Terminology

- **User Space** → Memory allocated for user processes.
- **Userland** → Informal term for user space and sometimes the programs running within it.

---

## Why User Space Matters

Most of the actual work on a Linux system happens in user space.

Examples:

- Web browsers
- Databases
- Web servers
- Desktop applications
- Shells
- Logging services

The kernel mostly provides resources and services, while user-space programs perform the tasks users interact with.

---

## User Space Layers

Although all processes are equal from the kernel's perspective, user-space components often form a loose service hierarchy.

### 1. Basic Services (Bottom Layer)

Small components that perform simple and specific tasks.

Examples:

- Diagnostic Logging
- Communication Bus
- Network Configuration

Characteristics:

- Lightweight
- Specialized
- Often provide services to higher layers

---

### 2. Utility Services (Middle Layer)

Medium-sized services that support applications.

Examples:

- Name Caching Server (DNS Cache)
- Mail Services
- Print Services
- Database Services

Characteristics:

- Used by multiple applications
- Provide common infrastructure services

---

### 3. User Applications (Top Layer)

Applications that users interact with directly.

Examples:

- Web Browser
- Desktop Environment
- User Interface Applications

Characteristics:

- Complex functionality
- Direct user interaction

---

## Example User Space Architecture

```text
               User Applications
           ┌──────────────────┐
           │   Web Browser    │
           │ User Interface   │
           └────────┬─────────┘
                    │
                    ▼

               Utility Services
           ┌──────────────────┐
           │ Name Cache Server│
           └────────┬─────────┘
                    │
                    ▼

               Basic Services
     ┌──────────────────────────────┐
     │ Network Configuration        │
     │ Communication Bus            │
     │ Diagnostic Logging           │
     └──────────────────────────────┘

                    ▼

                 Kernel
```

---

## Component Dependencies

User-space components frequently depend on one another.

General rule:

```text
Higher-Level Component
           ↓
 Uses Same-Level or
 Lower-Level Component
```

Example:

```text
Web Browser
     ↓
DNS Cache Server
     ↓
Network Services
```

---

## No Strict Rules in User Space

Unlike the kernel, user space has no strict architecture.

Different applications may implement functionality differently.

Example:

### Logging

Most Linux applications use:

```text
syslog
```

to write log messages.

However, some applications:

```text
Application
      ↓
Own Logging Mechanism
```

manage logging internally without relying on syslog.

---

## Difficulty in Categorizing Components

Some components can belong to multiple layers.

### Example: Database Servers

A database server can be viewed as:

#### High-Level Application

Because it performs complex operations.

Examples:

- MySQL
- PostgreSQL
- MongoDB

#### Utility Service

Because other applications depend on it.

```text
Web Application
       ↓
Database Server
```

Therefore user-space layers are conceptual rather than strict boundaries.

---

## Key Takeaways

- User space is the memory allocated for user processes.
- Most Linux system functionality runs in user space.
- User-space components can be viewed as basic services, utility services, and user applications.
- Higher-level components typically depend on lower-level components.
- User space has no strict architectural rules.
- Many services can fit into multiple categories depending on how they are used.

---

# 1.5 Users

## What is a User?

A **User** is an entity that can:

- Run processes
- Own files
- Access resources
- Be assigned permissions

Example:

```text
sai
john
admin
```

---

## Usernames vs User IDs (UID)

Humans use usernames:

```text
sai
john
root
```

The Linux kernel identifies users using numeric values called:

> User IDs (UIDs)

Example:

```text
sai   → UID 1000
john  → UID 1001
root  → UID 0
```

The kernel works with UIDs rather than usernames.

---

## Why Users Exist

Users provide:

### Security

Users are isolated from one another.

### Permissions

Access can be controlled for:

- Files
- Directories
- Processes
- Devices

### Ownership

Users can own:

- Files
- Directories
- Processes

---

## Process Ownership

Every process has an owner.

Example:

```text
User: sai

Processes:
├── bash
├── chrome
└── kubectl
```

These processes run as the user who started them.

---

## Process Restrictions

A normal user can:

- Start their own processes
- Stop their own processes
- Modify their own processes

A normal user cannot:

- Modify another user's processes
- Terminate another user's processes

Example:

```text
User A
   ✗
Cannot control
   ↓
User B's Process
```

---

## File Ownership

Users can own files and directories.

Example:

```text
/home/sai/notes.md
```

Owner:

```text
sai
```

The owner can determine who may access the file.

---

## Root User (Superuser)

The most important special user in Linux is:

```text
root
```

Root is known as the:

> Superuser

---

## Root Privileges

Root can:

- Access any file
- Modify any file
- Kill any process
- Change system configuration
- Install software
- Manage devices
- Create users
- Delete users

Example:

```text
root
  ↓
Complete Administrative Control
```

---

## Root UID

Root always has:

```text
UID 0
```

---

## Administrator

A person who can operate as root is called:

> System Administrator

or simply

> Administrator

---

## Important Note About Root

Although root is extremely powerful, it still runs in:

```text
User Mode
```

not:

```text
Kernel Mode
```

Root does not bypass the kernel.

Instead, the kernel grants root broader permissions.

---

## Risks of Using Root

Operating as root can be dangerous.

Mistakes can:

- Delete critical files
- Stop important services
- Damage system configuration
- Make the system unusable

Example:

```bash
rm -rf /
```

could potentially destroy the system if executed with sufficient privileges.

For this reason, modern Linux systems try to minimize the need for root access.

---

## Groups

A **Group** is a collection of users.

Example:

```text
developers
admins
docker
finance
```

Groups simplify permission management.

---

## Purpose of Groups

Instead of granting permissions individually:

```text
User A
User B
User C
```

permissions can be assigned to a group:

```text
developers
```

and all members inherit the access.

---

## Example

```text
Group: developers

Members:
├── sai
├── john
└── david
```

Shared access:

```text
/project/source-code
```

All members of the group can access the shared resources according to the assigned permissions.

---

# Key Takeaways

- A user is an entity that can run processes and own files.
- The Linux kernel identifies users through User IDs (UIDs).
- Every process runs as a particular user.
- Users own files and control access permissions.
- Normal users cannot interfere with another user's processes.
- Root (UID 0) is the superuser and has unrestricted administrative access.
- Root operates in user mode, not kernel mode.
- Groups are collections of users used to simplify permission management.

# Mental Model

```text
                     Linux System

                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │

        ▼                  ▼                  ▼

      Users            Groups           Processes

        │                  │                  │

        ▼                  ▼                  ▼

   File Ownership   Shared Access     Process Ownership

        │
        ▼

      Root
   (UID = 0)

        │
        ▼

 Full Administrative Control
```