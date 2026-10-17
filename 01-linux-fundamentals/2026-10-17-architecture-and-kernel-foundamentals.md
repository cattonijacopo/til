# Linux Architecture & Kernel Fundamentals

Key concepts from *How Linux Works* (Brian Ward) covering system layers, execution modes, and core kernel responsibilities.

---

## 1. System Layers & Execution Modes

Linux is organized into three main layers: **Hardware**, **Kernel**, and **User Space**.

* **Kernel Mode:**
  * Unrestricted access to CPU instructions and physical hardware.
  * Resides in dedicated, protected **Kernel Space**.
  * A crash here brings down the entire system.
* **User Mode:**
  * Restricted subset of CPU instructions and memory.
  * All user processes (shells, GUI, daemons/servers) run in **User Space**.
  * Process crashes are isolated and handled safely by the kernel.

---

## 2. Core Kernel Responsibilities

The kernel acts as the mediator between user processes and hardware, managing four key areas:

### A. Process Management & Context Switching
* **Multitasking:** Processes share CPU time via short intervals called **time slices**.
* **Context Switch:** The mechanism where the kernel pauses one process and swaps CPU execution to another.
  * The kernel executes **between** time slices during context switches.
  * Hardware timers trigger interrupts $\to$ switch to kernel mode $\to$ save CPU/memory state $\to$ select next process $\to$ restore state $\to$ switch back to user mode.

### B. Memory Management (Virtual Memory)
* Each process operates under the illusion that it owns the entire address space.
* The **MMU (Memory Management Unit)** translates virtual memory addresses to physical memory addresses via **page tables**.
* Guarantees memory isolation (processes cannot touch each other's memory unless explicitly shared) and kernel memory protection.

### C. Device Drivers
* Hardware devices are accessible only in kernel mode to prevent catastrophic direct access from user processes.
* Drivers abstract heterogeneous hardware into uniform programming interfaces.

### D. System Calls (Syscalls) & Process Lifecycle
User applications request kernel operations via syscalls. The standard lifecycle for starting new programs relies on two calls:

1. `fork()`: Creates a nearly identical child copy of the calling process.
2. `exec()`: Replaces the current process image with a new executable program.

> **Example (Running a command):**  
> `shell` $\xrightarrow{\text{fork()}}$ `copy of shell` $\xrightarrow{\text{exec(ls)}}$ `ls`

---

## 3. Users & Permissions
* **User:** An entity that can run processes and own files (identified internally by **UID**, not username).
* **Isolation:** Users cannot terminate or alter other users' processes.
* **Root (Superuser):** Bypasses standard permission boundaries (can access any local file and terminate any user process), but still executes in **User Mode**, not kernel mode.
