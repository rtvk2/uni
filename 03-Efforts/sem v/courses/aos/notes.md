___
# Chapter 1 — Introduction

## What an OS Does

- **OS** = resource allocator + control program; manages hardware, mediates between apps and hardware, aims for efficient/fair resource use.
- **4 components of a computer system**: Hardware (CPU, memory, I/O) → Operating System → Application programs → Users.
- View depends on perspective:
    - **User** (PC/workstation): wants convenience, ease, performance — doesn't care about utilization.
    - **Shared systems** (mainframe): OS must balance many users' needs.
    - **Mobile devices**: resource-poor, optimized for usability + battery.
    - **Embedded systems**: little/no UI, run without user intervention.

## Defining an OS

- No universal definition. Loose def: _"everything a vendor ships when you order an OS."_
- **Kernel** = the one program that runs at all times.
- **System program**: ships with OS, not part of kernel.
- **Application program**: not associated with the OS.
- **Middleware**: frameworks giving extra services (DB, multimedia, graphics) to app developers.

## Computer-System Organization

- One/more CPUs + device controllers connected via a **common bus** to shared memory; CPU and devices compete for memory cycles (concurrent execution).
- Each **device controller** manages one device type, has a **local buffer**.
- CPU moves data to/from main memory ↔ local buffers; actual I/O is device ↔ local buffer.
- Controller signals completion via an **interrupt**.

### Interrupts (important, testable)

- Interrupt → transfers control to **interrupt service routine (ISR)**, typically via the **interrupt vector** (holds addresses of all ISRs).
- Architecture must **save address of interrupted instruction**.
- **Trap/exception** = software-generated interrupt (error or explicit user request, e.g. system call).
- OS is fundamentally **interrupt-driven**.
- Interrupt handling steps: preserve CPU state (registers + PC) → determine interrupt type (**polling** or **vectored interrupt system**) → run separate code segment per interrupt type.

### Computer Startup

- **Bootstrap program**: stored in ROM/EPROM (**firmware**), runs at power-up/reboot, initializes system, loads OS kernel and starts it.

### I/O Structure

- **Synchronous I/O**: control returns to user program only after I/O completes; CPU may **wait instruction**-idle (wastes cycles) or busy-wait; at most one I/O outstanding at a time.
- **Asynchronous I/O**: control returns without waiting; **system call** used to explicitly wait for completion.
- **Device-status table**: tracks each device's type, address, state; OS updates it on interrupts.

## Storage Structure

- **Main memory**: only large storage the CPU can directly access; random access, typically volatile (DRAM).
- **Secondary storage**: nonvolatile, extends main memory.
    - **HDD**: magnetic platters; divided into **tracks → sectors**; disk controller handles device–computer interface.
    - **NVM (flash/SSD)**: faster than HDD, nonvolatile, gaining popularity.
- **Storage units**: bit → byte (8 bits) → word (native unit of a given architecture, e.g. 8-byte word on 64-bit systems). KB=1024B, MB=1024², GB=1024³ (network speeds measured in bits, not bytes).

### Storage Hierarchy

- Organized by **speed, cost, volatility** — registers > cache > main memory > SSD > HDD > optical/tape.
- **Caching**: copy data to faster storage temporarily (main memory acts as cache for secondary storage).
- Movement between levels can be **explicit or implicit**.
- Multiprocessor systems need **cache coherency** so all CPUs see the latest value; distributed systems face even harder consistency problems.

### Direct Memory Access (DMA)

- Used for high-speed I/O devices.
- Controller transfers data **directly to/from main memory**, bypassing CPU.
- Generates **one interrupt per block** transferred (not per byte) → big efficiency gain over programmed I/O.

## Computer-System Architecture

- Most systems: one general-purpose + several special-purpose processors.
- **Multiprocessor (parallel/tightly-coupled) systems** — advantages: increased throughput, economy of scale, increased reliability (graceful degradation/fault tolerance).
    - **Asymmetric multiprocessing (AMP)**: each processor assigned a specific task (e.g. one is "master").
    - **Symmetric multiprocessing (SMP)**: every processor runs any task, all peers.
- **Multicore**: multiple cores on one chip — faster than multiple chips, less power, but memory contention/cache-coherency issues.
- **NUMA (Non-Uniform Memory Access)**: memory access time depends on memory location relative to CPU.
- **Clustered systems**: multiple complete systems working together, usually sharing storage via **SAN**; gives high availability.
    - **Asymmetric clustering**: one machine in hot-standby.
    - **Symmetric clustering**: multiple nodes run apps and monitor each other.
    - Used for **HPC**; some use a **Distributed Lock Manager (DLM)** to prevent conflicting operations.

## Operating-System Operations

- **Dual-mode operation**: distinguishes **user mode vs kernel mode** via a hardware **mode bit**.
    - Privileged instructions execute **only in kernel mode**.
    - System call → switches to kernel mode; return resets to user mode.
    - Modern CPUs increasingly support **multi-mode** (e.g. VMM mode for guest VMs).
- **Timer**: prevents infinite loops/resource hogging — set to interrupt after a period; OS sets the counter (privileged); counter reaches 0 → interrupt → OS regains control.

### Multiprogramming vs Multitasking (Timesharing)

- **Multiprogramming**: keeps a subset of jobs in memory so CPU always has something to run; one job executes until it must wait (e.g. I/O), then OS switches — needs **job scheduling**.
- **Multitasking (timesharing)**: extension — CPU switches jobs so fast users interact with each (interactive computing); response time should be **< 1 sec**; needs **CPU scheduling** if multiple jobs are ready; **swapping** moves processes in/out if they don't fit; **virtual memory** allows executing a process not fully loaded in memory.

## Resource Management (OS Responsibilities)

- **Process management**: create/delete processes, suspend/resume, synchronization, communication, deadlock handling.
    - Process = program **in execution** (active entity) vs. program (passive entity).
    - Single-threaded: 1 program counter; multi-threaded: 1 pc per thread.
- **Memory management**: track what's in memory & by whom; decide what moves in/out; allocate/deallocate memory.
- **File-system management**: uniform logical view of storage (files); organize into directories; access control; create/delete, map onto storage, backups.
- **Mass-storage management**: mounting/unmounting, free-space mgmt, allocation, disk scheduling, partitioning, protection. **Tertiary storage** (optical, tape) — slower but still needs management.
- **Caching**: copy data from slower→faster storage; check cache first (hit = fast path; miss = copy in and use); cache is smaller than what it caches; key design issues = **cache size & replacement policy**.
- **I/O subsystem**: hides hardware peculiarities; handles buffering, caching, spooling (overlap output of one job with input of others); provides generic device-driver interface.

## Protection and Security

- **Protection**: mechanism to control process/user access to OS-defined resources.
- **Security**: defense against internal & external attacks (DoS, worms, viruses, identity theft, theft of service).
- **User ID / Security ID**: identifies each user; associated with their files/processes for access control.
- **Group ID**: allows grouping of users for shared control.
- **Privilege escalation**: user switches to an effective ID with more rights.

## Virtualization

- Runs OSes/apps within other OSes.
- **Emulation**: source CPU type ≠ target type (e.g. PowerPC → x86) — slowest method.
- **Interpretation**: language not compiled to native code.
- **Virtualization proper**: both host and guest OS are natively compiled for the same CPU type.
- **VMM (Virtual Machine Manager)**: provides virtualization; can run natively as the host itself (no general-purpose host) — e.g. VMware ESX, Citrix XenServer.
- Use cases: multi-OS testing/dev, running apps needing another OS, managing data-center compute environments.

## Distributed Systems

- Collection of separate (possibly heterogeneous) systems networked together; illusion of a single system.
- Network types: **LAN, WAN, MAN, PAN**; TCP/IP most common protocol.
- **Network OS**: provides cross-system features, allows message exchange between systems.

## Kernel Data Structures

- Standard structures reused in kernels: **singly/doubly/circular linked lists**.
- **Binary search tree**: left ≤ node ≤ right; search O(n) worst case, **O(log n) if balanced**.
- **Hash map**: uses a hash function for fast lookup.
- **Bitmap**: string of n binary digits representing status of n items.
- Linux structures defined in `<linux/list.h>`, `<linux/kfifo.h>`, `<linux/rbtree.h>`.

## Computing Environments

- **Traditional**: standalone machines, now blurred by internet interconnection; portals, thin clients, home firewalls.
- **Mobile**: smartphones/tablets — extra OS features (GPS, gyroscope) enable new app types (AR); use 802.11 wireless / cellular; dominant: **iOS, Android**.
- **Client-Server**: servers respond to client requests — **compute-server** (e.g. DB) vs **file-server** systems.
- **Peer-to-Peer (P2P)**: no client/server distinction — all nodes are peers, can act as client/server/both; must register with a lookup service **or** broadcast/discover. Examples: Napster, Gnutella, Skype (VoIP).
- **Cloud Computing**: delivers compute/storage/apps as a service over network; logical extension of virtualization.
    - **Public cloud**: available to anyone who pays.
    - **Private cloud**: run by/for one company.
    - **Hybrid cloud**: mix of public + private.
    - **SaaS**: apps over internet (e.g. word processor).
    - **PaaS**: ready software stack (e.g. DB server).
    - **IaaS**: raw servers/storage (e.g. backup storage).
    - Composed of traditional OSes + VMMs + cloud management tools; needs firewalls, **load balancers** to spread traffic.
- **Real-Time Embedded Systems**: most prevalent computing form; often special/limited-purpose or **real-time OS**; correctness depends on meeting fixed time constraints.

## Free / Open-Source Operating Systems

- Source code made available (not just binary); counters DRM/copy-protection movements.
- Started by **Free Software Foundation (FSF)**; **GPL** ("copyleft") license.
- "Free software" and "open-source software" are **distinct philosophies/movements**.
- Examples: **GNU/Linux, BSD UNIX** (core of Mac OS X).
- VMMs for exploring other OSes: **VMware Player** (free, Windows), **VirtualBox** (free & open source, cross-platform).

---

> [!note] Likely exam angles
> 
> - Explain user-mode ↔ kernel-mode transition steps.
> - Interrupt-driven nature of OS; polling vs vectored interrupts.
> - DMA vs programmed I/O (why DMA is more efficient).
> - AMP vs SMP; symmetric vs asymmetric clustering.
> - Multiprogramming vs multitasking/timesharing — definitions & differences.
> - SaaS vs PaaS vs IaaS with examples.
> - Storage hierarchy ordering and caching principle.

---
# Chapter 2 — Operating-System Structures

## OS Services — User-Helpful

- **User interface (UI)**: CLI, GUI, touch-screen, batch.
- **Program execution**: load program into memory, run it, end execution (normal/abnormal/error).
- **I/O operations**: managed on behalf of running programs (file or device).
- **File-system manipulation**: read/write/create/delete/search files & dirs, permission management.
- **Communications**: between processes on same computer or over a network — via **shared memory** or **message passing** (OS-moved packets).
- **Error detection**: OS must detect errors in CPU/memory hardware, I/O devices, or user programs and take correct action.
- **Debugging facilities**: help users/programmers use system efficiently.

## OS Services — System-Efficiency (not user-facing)

- **Resource allocation**: distribute CPU cycles, memory, storage, I/O devices among concurrent users/jobs.
- **Logging/accounting**: track resource usage per user.
- **Protection and security**: control access to stored info; prevent processes interfering with each other; secure system from outside attackers (needs authentication).

## User–OS Interface

- **CLI (command interpreter/shell)**: fetches and executes commands; may be in kernel or a systems program; multiple shell "flavors" possible. If commands = just program names, adding features needs no shell modification.
- **GUI**: desktop metaphor, mouse+keyboard+monitor, icons; invented at **Xerox PARC**.
    - Windows = GUI + CLI ("command" shell).
    - Mac OS X = "Aqua" GUI over UNIX kernel, shells available.
    - UNIX/Linux = CLI-first with optional GUI (CDE, KDE, GNOME).
- **Touchscreen**: gesture-based, virtual keyboard, voice commands — no mouse.

## System Calls

- Programming interface to OS services; usually written in C/C++.
- Apps normally use a high-level **API**, not raw system calls directly.
- Three major APIs: **Win32** (Windows), **POSIX** (UNIX/Linux/Mac OS X), **Java API** (JVM).
- **Standard C library example**: `printf()` (library call) internally invokes the `write()` system call.

### System Call Implementation

- Each system call has an associated **number**; system-call interface uses a **table** indexed by these numbers.
- Interface invokes the correct kernel routine, returns status + return values.
- Caller doesn't need to know implementation — just the API contract.
- Managed by the **run-time support library**.

### Parameter Passing (3 methods)

1. **Registers** — simplest, but limited by register count.
2. **Block/table in memory** — address of block passed in a register (used by **Linux, Solaris**); no limit on number/length of parameters.
3. **Stack** — parameters pushed by program, popped by OS; also no size limit.

### Types of System Calls

- **Process control**: create/terminate process, load/execute, get/set attributes, wait for time/event, signal event, allocate/free memory, dump memory, debugger, locks.
- **File management**: create/delete, open/close, read/write/reposition, get/set attributes.
- **Device management**: request/release device, read/write/reposition, get/set attributes, attach/detach.
- **Information maintenance**: get/set time/date, get/set system data, get/set process/file/device attributes.
- **Communications**: create/delete connection, send/receive messages (message-passing model), shared-memory model (create/access memory regions), transfer status info, attach/detach remote devices.
- **Protection**: control resource access, get/set permissions, allow/deny access.

### Worked Examples (concept, not memorize verbatim)

- **Arduino**: no OS, single-tasking, single memory space; program (sketch) loaded via USB into flash; bootloader loads program; on exit, shell reloads.
- **FreeBSD**: multitasking; user login → shell; shell does `fork()` (create process) → `exec()` (load program into that process); shell waits or continues; process exits with code 0 (success) or >0 (error).

## System Services (System Programs)

- Provide a convenient environment for program development/execution; **most users experience the OS through these, not raw system calls**.
- Categories:
    - **File management** — create/delete/copy/rename/print/list files & dirs.
    - **Status information** — date, time, memory/disk space, users; some systems use a **registry** for config storage.
    - **File modification** — text editors, search/transform tools.
    - **Programming-language support** — compilers, assemblers, debuggers, interpreters.
    - **Program loading & execution** — absolute/relocatable loaders, linkage editors, overlay-loaders, debuggers.
    - **Communications** — virtual connections between processes/users/systems (messaging, browsing, email, remote login, file transfer).
    - **Background services (daemons)** — launch at boot, run in **user context** (not kernel); disk checking, scheduling, error logging, printing.
    - **Application programs** — not part of the OS; launched by user action (CLI, click, tap).

## Linkers and Loaders

- Source compiled → **relocatable object files** (loadable at any memory location).
- **Linker** combines object files (+ libraries) into a single binary executable.
- **Loader** brings the executable from secondary storage into memory to run.
- **Relocation**: assigns final addresses, adjusts code/data accordingly.
- Modern systems use **dynamically linked libraries (DLLs on Windows)** — loaded as needed, shared across all programs using the same version (loaded once).
- Standard object/executable file formats let the OS know how to load/start programs.

## Why Applications Are OS-Specific

- Each OS has unique system calls & file formats → binaries not portable across OSes.
- Cross-platform options:
    - **Interpreted languages** (Python, Ruby) — interpreter exists per OS.
    - **VM-based languages** (Java) — app runs inside a VM present on each OS.
    - **Compile separately** per OS from standard source (e.g. C).
- **ABI (Application Binary Interface)**: architecture-level equivalent of an API — defines binary code interfacing for a given OS+CPU architecture.

## OS Design and Implementation

- No single "solvable" design approach; internal structures vary widely.
- Design starts from **goals and specifications**, shaped by hardware/system type.
- **User goals**: convenient, easy to learn, reliable, safe, fast.
- **System goals**: easy to design/implement/maintain, flexible, reliable, error-free, efficient.
- **Policy vs Mechanism** (key separation principle):
    - **Policy** = _what_ will be done.
    - **Mechanism** = _how_ it's done.
    - Separating them allows changing policy later without changing mechanism (e.g., timer mechanism vs. scheduling policy).
- **Implementation languages**: early OSes = assembly; then Algol/PL-1 style; now mostly **C/C++**, mixed with assembly (lowest level) and scripting (Perl/Python/shell) for system programs.
    - Higher-level language → easier portability, but slower.
    - **Emulation** lets an OS run on non-native hardware.

## Operating System Structure

### 1. Simple / Monolithic — e.g. MS-DOS, original UNIX

- UNIX originally = **system programs + kernel**; kernel = everything below system-call interface, above hardware (file system, CPU scheduling, memory mgmt, etc. — large single layer of functionality).

### 2. Layered Approach

- OS split into N layers; **layer 0 = hardware, layer N = user interface**.
- Each layer only uses functions/services of layers **below** it.

### 3. Microkernels — e.g. Mach (Mac OS X/Darwin partly based on it)

- Moves as much as possible **out of the kernel into user space**.
- Communication between user modules via **message passing**.
- **Benefits**: easier to extend, easier to port, more reliable (less kernel-mode code), more secure.
- **Drawback**: performance overhead from user↔kernel space communication.

### 4. Modules (Loadable Kernel Modules, LKMs)

- Object-oriented; each core component separate, communicates over known interfaces, loaded as needed.
- Similar to layers but more flexible. Used by **Linux, Solaris**.

### 5. Hybrid — most modern OSes

- **Linux/Solaris**: monolithic (kernel in kernel address space) + modular (dynamic loading).
- **Windows**: mostly monolithic + microkernel-style subsystem "personalities."
- **Mac OS X**: layered hybrid — Aqua UI + Cocoa env on top of a kernel combining **Mach microkernel + BSD Unix** + I/O Kit + dynamically loadable kernel extensions.

### Mobile OS Structures

- **iOS**: built on Mac OS X kernel, ARM architecture (not Intel), cannot run OS X apps natively; layers: Cocoa Touch (Objective-C API), Media services, Core services (cloud, DB), Core OS.
- **Android**: developed by Open Handset Alliance (mainly Google), open source; based on **modified Linux kernel** (process/memory/driver mgmt + power mgmt added); apps in Java + Android API, compiled to bytecode, run on **Dalvik VM**; libraries include WebKit (browser), SQLite (DB), smaller libc.

## Building and Booting an OS

- General build steps: write source → configure for target system → compile → install → boot.
- **Linux build example**: get source (kernel.org) → `make menuconfig` (configure) → `make` (compile, produces `vmlinuz`) → `make modules` → `make modules_install` → `make install`.

### System Boot

- Execution starts at a **fixed memory location** on power-up.
- **Bootstrap loader / BIOS** (in ROM/EEPROM) locates kernel, loads into memory, starts it.
- Sometimes two-step: ROM code loads a **boot block** from a fixed disk location, which loads the real bootstrap loader.
- Modern replacement for BIOS: **UEFI**.
- Common bootloader: **GRUB** — lets you pick kernel/version/options across disks.
- Boot loaders may offer boot states like **single-user mode**.

## Operating-System Debugging

- **Debugging** = finding/fixing bugs; also covers performance tuning.
- OS generates **log files** with error info.
- **Core dump**: captures a crashed application's memory.
- **Crash dump**: captures kernel memory on OS failure.
- **Trace listings**: record activity sequences for analysis.
- **Profiling**: periodic sampling of instruction pointer to find statistical hotspots.
- Kernighan's Law: debugging is harder than writing code — don't over-engineer cleverness.

### Performance Tuning & Tracing Tools

- Goal: remove bottlenecks; tools show system behavior (e.g. **top**, Windows Task Manager).
- **strace**: trace system calls of a process.
- **gdb**: source-level debugger.
- **perf**: Linux performance tool collection.
- **tcpdump**: captures network packets.
- **BCC (BPF Compiler Collection)**: tracing toolkit understanding both user & kernel level (e.g. `disksnoop.py` traces disk I/O); related to original **DTrace**.

---

> [!note] Likely exam angles
> 
> - Full list + categories of system-call types (process/file/device/info/comm/protection) with 1–2 examples each.
> - Steps of system call implementation (number → table → invoke → return).
> - The 3 parameter-passing methods and which OS uses which.
> - Compare monolithic vs layered vs microkernel vs modular vs hybrid — advantages/drawbacks.
> - Policy vs Mechanism definition + why separating them matters.
> - Boot sequence order: power-on → bootstrap/BIOS/UEFI → boot loader (GRUB) → kernel load.
> - iOS vs Android structural differences.
> - Difference between system calls, APIs, and system programs (system services).