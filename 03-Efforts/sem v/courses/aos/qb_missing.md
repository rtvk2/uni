**Missing Question 1: I/O Structure and Device-Status Table**

**Q: Describe the two methods by which control is returned to a user program after an I/O operation starts, and explain the purpose of the device-status table.**

**Answer:**

* After I/O starts, control can return to the user program only upon I/O completion, where a wait instruction idles the CPU until the next interrupt.
* Alternatively, control can return to the user program without waiting for I/O completion, using a system call to request the operating system to allow the user to wait for I/O completion.
* The device-status table contains an entry for each I/O device indicating its type, address, and state.
* The operating system indexes into the I/O device table to determine the device status and modifies the table entry to include the interrupt.

- **Synchronous I/O**: Control returns to the user program _only after_ the I/O completes.  This can be implemented either as a **busy-wait loop** (CPU repeatedly polls the device status) or as a **wait instruction** that idles the CPU until the next interrupt. Both count as synchronous — the user program makes no progress until I/O finishes.
- **Asynchronous I/O**: Control returns to the user program **immediately**, without waiting for I/O to finish. The system call in this case just _initiates_ the I/O and returns right away, letting the user program keep running. A **separate** system call (like `wait` or checking a completion flag) is used later if/when the program actually needs to block until that I/O is done. Control returns immediately via one system call, and a _different_ system call is available later to explicitly wait for completion.

---

**Missing Question 2: Clustered Systems**

**Q: What are clustered systems, and how do asymmetric and symmetric clustering differ?**

**Answer:**

* Clustered systems are like multiprocessor systems, but consist of multiple systems working together.
* They usually share storage via a storage-area network (SAN) and provide a high-availability service which survives failures.
* Asymmetric clustering has one machine in hot-standby mode.
* Symmetric clustering has multiple nodes running applications and monitoring each other.
* Some clusters are designed for high-performance computing (HPC), requiring applications to be written to use parallelization.
* Some clustered systems use a distributed lock manager (DLM) to avoid conflicting operations.

---

**Missing Question 3: Operating System Management Activities**

**Q: What specific management activities is the operating system responsible for regarding memory, file-systems, and mass-storage?**

**Answer:**

* **Memory management activities:** Keeping track of which parts of memory are currently being used and by whom, deciding which processes (or parts thereof) and data to move into and out of memory, and allocating and deallocating memory space as needed.
* **File-system management activities:** Creating and deleting files and directories, providing primitives to manipulate files and directories, mapping files onto secondary storage, and backing up files onto stable (non-volatile) storage media.
* **Mass-storage management activities:** Mounting and unmounting, free-space management, storage allocation, disk scheduling, partitioning, and protection.

---

**Missing Question 4: Kernel Data Structures**

**Q: List and describe the standard kernel data structures commonly used in operating systems.**

**Answer:**

* **Linked lists:** Include singly linked lists, doubly linked lists, and circular linked lists.
* **Binary search trees:** Offer search performance of $O(n)$, while a balanced binary search tree provides performance of $O(\lg n)$.
* **Hash maps:** Created by utilizing a hash function on a key to map to a value.
* **Bitmaps:** A string of $n$ binary digits representing the status of $n$ items.

---

**Missing Question 5: Traditional vs. Mobile Computing Environments**

**Q: What are the defining characteristics of Traditional and Mobile computing environments?**

**Answer:**

* **Traditional computing:** Consists of stand-alone general purpose machines, though boundaries are blurred as most systems interconnect with others via the Internet. Portals provide web access to internal systems, network computers (thin clients) act like Web terminals, and networking is becoming ubiquitous.
* **Mobile computing:** Includes handheld smartphones and tablets, which differ from traditional laptops by offering extra features and more operating system features like GPS and gyroscopes. They allow for new types of apps, such as augmented reality, and use IEEE 802.11 wireless or cellular data networks for connectivity.

---

**Missing Question 6: Windows vs. UNIX System Call Equivalents**

**Q: Provide examples of equivalent system calls in Windows and UNIX for file management, device management, and communications.**

**Answer:**

* **File management:** Windows utilizes `CreateFile()`, `ReadFile()`, `WriteFile()`, and `CloseHandle()`, whereas UNIX utilizes `open()`, `read()`, `write()`, and `close()`.
* **Device management:** Windows utilizes `SetConsoleMode()`, `ReadConsole()`, and `WriteConsole()`, whereas UNIX utilizes `ioctl()`, `read()`, and `write()`.
* **Communications:** Windows utilizes `CreatePipe()`, `CreateFileMapping()`, and `MapViewOfFile()`, whereas UNIX utilizes `pipe()`, `shm_open()`, and `mmap()`.



---

**Missing Question 7: The Standard C Library Interface**

**Q: How does the standard C library interface with operating system calls? Provide an example.**

**Answer:**

* The standard C library provides a portion of the system-call interface for many versions of UNIX and Linux.
* The C library intercepts function calls from the user program and invokes the necessary system call (or calls) in the operating system.
* If a C program invokes the `printf()` statement, the C library intercepts this call and invokes the `write()` system call in the kernel.
* The C library takes the value returned by `write()` and passes it back to the user program.


---

**Missing Question 8: Single-tasking vs. Multitasking Execution Environments**

**Q: Compare how a single-tasking system (like Arduino) and a multitasking system (like FreeBSD) load and execute programs.**

**Answer:**

* **Arduino (Single-tasking):** Has no operating system, and programs (sketches) are loaded via USB into flash memory by a boot loader. The program operates in a single memory space alongside the boot loader and free memory.

* **FreeBSD (Multitasking):** A Unix variant that invokes the user's choice of shell upon login. The shell executes the `fork()` system call to create a process, then executes `exec()` to load a program into that process. The shell waits for the process to terminate or continues with user commands. When the process exits, it returns code $=0$ for no error or a code $>0$ for an error code.


**Missing Question 9: The von Neumann Architecture and Instruction Cycle**

**Q: How does a modern computer function under the von Neumann architecture regarding instruction execution and data movement?**

**Answer:**
* A modern computer relies on an instruction execution cycle where the CPU interacts with memory to fetch instructions and data.
* The CPU maintains a thread of execution, moving data between memory, its cache, and registers.
* I/O devices can transfer data directly to memory via Direct Memory Access (DMA), bypassing the CPU for the data movement itself.
* The devices generate an interrupt to the CPU to signal when an I/O request is complete.

---

**Missing Question 10: Data Migration and Cache Coherency**

**Q: What specific challenges arise regarding data migration across the storage hierarchy in multitasking and multiprocessor environments?**

**Answer:**

* As data (e.g., value "A") migrates from a magnetic disk to main memory, then to cache, and finally to a hardware register, a multitasking environment must be careful to use the most recent value regardless of where it currently resides in the hierarchy.
* In a multiprocessor environment, the situation is more complex, requiring cache coherency implemented in hardware to ensure that all CPUs have the most recent value in their individual caches.
* In distributed environments, several copies of a datum can exist, making the synchronization situation even more complex.

---

**Missing Question 11: Advanced Tracing and BCC**

**Q: Why are advanced toolsets like the BPF Compiler Collection (BCC) necessary for operating system debugging, and how do they function?**

**Answer:**

* Debugging interactions between user-level and kernel code is nearly impossible without a toolset that understands both and can instrument their actions.
* BCC is a rich toolkit that provides these advanced tracing features for Linux, conceptually similar to the original DTrace.
* It includes numerous utilities for tracing system behaviors across applications, system libraries, the system call interface, and device drivers.
* A specific example is `disksnoop.py`, which is used to trace disk I/O activity, capturing metrics like bytes transferred and latency.


**Missing Question 12: I/O Subsystem Responsibilities**

**Q: What are the primary responsibilities of the I/O subsystem in an operating system?**

**Answer:**

* One of the main purposes of the operating system is to hide the peculiarities of hardware devices from the user.
* The I/O subsystem handles memory management of I/O, which includes **buffering** (temporarily storing data while it is being transferred), **caching** (storing parts of data in faster storage for performance), and **spooling** (the overlapping of output of one job with input of other jobs).
* It also provides a general device-driver interface and manages drivers for specific hardware devices.

---

**Missing Question 13: Distributed Systems and Networks**

**Q: How does a distributed computing system function and what are the common network types used to connect them?**

**Answer:**
* A distributed system is a collection of separate, possibly heterogeneous systems that are networked together to provide the illusion of a single system.
* Network communication allows the systems to exchange messages, typically using TCP/IP across various network types, including Local Area Networks (LAN), Wide Area Networks (WAN), Metropolitan Area Networks (MAN), and Personal Area Networks (PAN).
* A Network Operating System provides the features needed to connect these distinct systems across the network.

