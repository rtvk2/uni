# Chapter 4: Threads and Concurrency - Complete Notes

These notes are formatted for voice reading.

Where the original notes contained a diagram, refer to the diagram on the indicated slide.
Where the original notes contained code, refer to the code on the indicated slide.

## Part 1: Overview (Slides 1-7)

### Slide 1: Title
Chapter 4: Threads & Concurrency  -  Silberschatz, Galvin, Gagne.

### Slide 2: Outline
1. Overview
2. Multicore Programming
3. Multithreading Models
4. Thread Libraries
5. Implicit Threading
6. Threading Issues
7. Operating System Examples

Exam tip: The outline is the skeleton of every exam question.

### Slide 3: Objectives
- Identify basic components of a thread, and contrast threads vs processes.
- Describe benefits and challenges of multithreaded applications.
- Illustrate implicit threading approaches: thread pools, fork-join, Grand Central Dispatch.
- Describe how Windows and Linux represent threads.
- Design multithreaded applications using Pthreads, Java, and Windows threading APIs.

### Slide 4: Why Multithreading?
- Most modern applications are multithreaded.
- Threads run within an application.
- Multiple tasks inside one app can be separate threads:
  - Update display
  - Fetch data
  - Spell checking
  - Answer a network request
- Process creation is heavyweight; thread creation is lightweight.
- Can simplify code and increase efficiency.
- Kernels are generally multithreaded.

Intuition: A process is a house; threads are people living in it. They share the kitchen (memory) but each has their own chair (stack, registers, PC).

### Slide 5: Single vs Multithreaded Process (Diagram)

Refer to the diagram on Slide 5.

Key distinction:
- Shared across threads: code section, data section, open files, signals, address space.
- Private per thread: thread ID, program counter (PC), register set, stack.

Trap: Exam often asks "Which is not shared?" Answer: stack, registers, PC.

### Slide 6: Server Thread Model (Diagram)
1. Client sends request.
2. Server creates a new thread to service the request.
3. Server resumes listening for additional client requests.

Refer to the diagram on Slide 6.

Why this matters: Classic concurrent server pattern. Single-threaded server blocks on one request; multithreaded handles many clients concurrently.

### Slide 7: Benefits of Multithreading
1. Responsiveness  -  if one thread blocks, others continue; critical for user interfaces.
2. Resource Sharing  -  threads share the process's memory by default; easier than shared memory or message passing (Chapter 3 IPC).
3. Economy  -  thread creation is cheaper than process creation; thread switching has lower overhead than context switching.
4. Scalability  -  a process can exploit multicore architectures.

Mnemonic: R-R-E-S (Responsiveness, Resource sharing, Economy, Scalability).

Exam trap: Do not say threads are always faster. They are cheaper and more scalable, but correctness requires synchronization.

### Part 1 Summary
- Process = heavy; Thread = light.
- Thread shares: code, data, files.
- Thread owns: registers, PC, stack.
- Server model: request to new thread to resume listening.
- Benefits: R-R-E-S.
- Kernel is multithreaded.

## Part 2: Multicore Programming & Amdahl’s Law (Slides 8-13)

### Slide 8: Multicore Programming  -  Challenges
1. Dividing activities  -  split work into independent tasks.
2. Balance  -  ensure cores do roughly equal work.
3. Data splitting  -  partition data correctly among tasks.
4. Data dependency  -  tasks may need results from others; must synchronize.
5. Testing and debugging  -  concurrent bugs are non-deterministic and hard to reproduce.

Parallelism vs Concurrency:
- Concurrency  -  supports more than one task making progress. A single-core scheduler gives concurrency by time-slicing.
- Parallelism  -  system can perform more than one task simultaneously. Requires multiple cores.

Trap: Concurrency is not equal to Parallelism. Single core can be concurrent but never parallel.

### Slide 9: Concurrent vs Parallel Execution (Diagram)

Single-core (concurrent):
Refer to the diagram on Slide 9.
Only one task runs at any instant; scheduler interleaves.

Multi-core (parallel):
Refer to the diagram on Slide 9.
Tasks run simultaneously on different cores.

Key point: Parallelism requires hardware (multiple cores); concurrency only requires a scheduler.

### Slide 10: Types of Parallelism
Data parallelism:
- Distribute subsets of the same data across multiple cores.
- Same operation on each subset.
- Example: adding two arrays element-wise; each core handles a chunk.

Task parallelism:
- Distribute threads across cores.
- Each thread performs a unique operation.
- Example: one thread updates display, another fetches data, another spell-checks.

Formal distinction:
- Data parallelism: same code, different data.
- Task parallelism: different code, possibly same/different data.

### Slide 11: Data and Task Parallelism (Diagram)

Data parallelism:
Refer to the diagram on Slide 11.
One data block split; each core applies same operation.

Task parallelism:
Refer to the diagram on Slide 11.
Different tasks/operations per core, possibly on same data.

Label: data parallelism = same operation on subsets; task parallelism = unique operations per thread.

### Slide 12: Amdahl’s Law
Formula:
Speedup is less than or equal to 1 divided by S plus (1 minus S) divided by N.

Variables:
- S = serial fraction of the application, with S between 0 and 1.
- 1 - S = parallel fraction
- N = number of processing cores
- Speedup = old execution time / new execution time

When it applies: Any program with a portion that cannot be parallelized.

Example from slide:
- Application is 75% parallel and 25% serial, so S = 0.25 and the parallel portion is 0.75.
- Moving from 1 core to 2 cores:

Speedup is less than or equal to 1 divided by 0.25 plus 0.75 divided by 2.
= 1 divided by 0.25 + 0.375
= 1 divided by 0.625
= 1.6

So speedup is approximately 1.6 times.

As N approaches infinity:
Formula: Speedup approaches 1 divided by S
For S = 0.25, maximum speedup = 1/0.25 = 4.
Serial portion has disproportionate effect  -  even infinite cores cannot overcome serial bottleneck.

Slide’s open question: “But does the law take into account contemporary multicore systems?”
Answer: It is an ideal upper bound. Real systems have overhead, memory stalls, synchronization, and non-serializable work, so actual speedup is often lower.

### Slide 13: Speedup vs Number of Cores (Graph)

Refer to the graph on Slide 13.

- x-axis: Number of processing cores (0 to 16)
- y-axis: Speedup (0 to 16)
- Curve rises quickly then flattens toward 1/S.

Interpretation:
- Adding cores gives diminishing returns.
- The asymptote 1/S is the hard limit.
- For S = 0.25, curve flattens near speedup = 4 even at 16 cores.

Exam shortcut: If given S and N, plug into Amdahl’s formula. If asked maximum speedup, compute 1/S.

### Part 2 Summary
- Five multicore challenges: Dividing, Balance, Data splitting, Data dependency, Testing/debugging.
- Concurrency = interleaving; Parallelism = simultaneous.
- Data parallelism = same op, different data; Task parallelism = different ops.
- Amdahl: Speedup less than or equal to 1 divided by S + (1-S)/N.
- Max speedup = 1/S as N approaches infinity.
- Graph: speedup vs cores flattens toward 1/S.

## Part 3: Thread Types & Multithreading Models (Slides 14-20)

### Slide 14: User Threads vs Kernel Threads
User threads
- Managed by a user-level threads library.
- Kernel is unaware of them.
- Creation/switching is fast (no kernel involvement).
- If one blocks, kernel may block whole process (depending on model).

Kernel threads
- Supported directly by the kernel.
- Kernel schedules them.
- Creation/switching requires system calls to slower.
- Blocking one thread does not necessarily block others.

Three primary thread libraries (user-level):
1. POSIX Pthreads
2. Windows threads
3. Java threads

Kernel thread examples: virtually all general-purpose OSes  -  Windows, Linux, Mac OS X, iOS, Android.

Exam trap: Pthreads is a specification, not an implementation. It can be user-level or kernel-level depending on OS.

### Slide 15: Transition Slide
- No new content. Marks the shift from thread basics to multithreading models.
- Slide number 4.15, Silberschatz, Galvin and Gagne ©2018.

### Slide 16: Three Multithreading Models
1. Many-to-One
2. One-to-One
3. Many-to-Many

Mnemonic: M:1, 1:1, M:M. Two-level is a variant of M:M (Slide 20).

### Slide 17: Many-to-One Model
Refer to the diagram on Slide 17.
Definition: Many user-level threads mapped to one kernel thread.

Diagram to draw and label:
Refer to the diagram on Slide 17.

Properties:
- One thread blocking causes all to block.
- Multiple threads cannot run in parallel on multicore  -  only one may be in kernel at a time.
- Few systems currently use this model.

Examples:
- Solaris Green Threads
- GNU Portable Threads

Trap: Many-to-One gives concurrency but not parallelism.

### Slide 18: One-to-One Model
Refer to the diagram on Slide 18.
Definition: Each user-level thread maps to one kernel thread.

Diagram to draw and label:
Refer to the diagram on Slide 18.

Properties:
- Creating a user-level thread creates a kernel thread.
- More concurrency than many-to-one.
- Number of threads per process sometimes restricted due to overhead.
- Blocking one thread does not block others.

Examples:
- Windows
- Linux

Trap: One-to-One is simple but can be expensive if you create thousands of threads. Thread pools solve this.

### Slide 19: Many-to-Many Model
Refer to the diagram on Slide 19.
Definition: Many user-level threads mapped to many kernel threads.

Diagram to draw and label:
Refer to the diagram on Slide 19.

Properties:
- Allows many user-level threads to be mapped to many kernel threads.
- OS can create a sufficient number of kernel threads.
- If one thread blocks, others can still run.
- Windows with the ThreadFiber package is an example.
- Otherwise not very common in modern OSes (most use One-to-One).

Trap: Many-to-Many requires a multiplexing mechanism (often LWP - lightweight processes).

### Slide 20: Two-Level Model
Refer to the diagram on Slide 20.
Definition: Similar to M:M, except that it allows a user thread to be bound to a kernel thread.

Diagram to draw and label:
Refer to the diagram on Slide 20.

Key difference from M:M:
- Most user threads are multiplexed over kernel threads.
- One or more user threads can be bound to a specific kernel thread (shown by the vertical line on the right).
- Bound threads are always scheduled on their assigned kernel thread.

Why? Some threads need guaranteed kernel-level scheduling (e.g., real-time threads).

Examples: IRIX, HP-UX, and some older systems.

Exam trap: Two-level = M:M + binding. It is not a separate mapping; it’s a hybrid.

### Part 3 Summary

Many-to-One. User:Kernel: M:1. Blocking: One blocks all. Parallel?: No. Examples: Solaris Green, GNU Pth
One-to-One. User:Kernel: 1:1. Blocking: Independent. Parallel?: Yes. Examples: Windows, Linux
Many-to-Many. User:Kernel: M:M. Blocking: Independent. Parallel?: Yes. Examples: ThreadFiber (Windows)
Two-Level. User:Kernel: M:M + bind. Blocking: Independent. Parallel?: Yes. Examples: IRIX, HP-UX

Mnemonics:
- M:1 = “Many users, one kernel” to blocking problem.
- 1:1 = “One for one” to simple, expensive.
- M:M = “Many for many” to flexible, complex.
- Two-level = M:M with VIP binding.

Common exam trap: “Which model allows true parallelism?” to One-to-One and Many-to-Many (and Two-Level). Many-to-One does not.

## Part 4: Thread Libraries & Code Tracing (Slides 21-32)

### Slide 21: Thread Library Implementation
A thread library provides the programmer with an API for creating and managing threads.

Two primary ways to implement:
1. Library entirely in user space  -  no kernel support. Fast, but blocking one thread can block all; kernel is unaware.
2. Kernel-level library supported by the OS  -  system calls. Slower but true parallelism and independent blocking.

Trap: Pthreads is a specification; it can be implemented either way.

### Slide 22: Pthreads
- POSIX standard (IEEE 1003.1c) API for thread creation and synchronization.
- Specification, not implementation  -  API specifies behavior; implementation is up to the library.
- Common in UNIX operating systems (Linux & Mac OS X).

Exam tip: Pthreads is not a language; it’s a C library standard.

### Slide 23: Pthreads  -  Main Code
Refer to the code on Slide 23.

Line-by-line trace:
- int sum;  -  global variable, shared by all threads.
- pthread_t tid;  -  holds thread ID.
- pthread_attr_t attr;  -  thread attributes.
- pthread_attr_init(&attr);  -  initialize attributes to defaults.
- pthread_create(&tid, &attr, runner, argv[1]);  -  creates a new thread that runs runner with parameter argv[1].
- pthread_join(tid, NULL);  -  blocks main thread until tid terminates.
- printf prints final sum.

Trap: If pthread_join is omitted, main may exit before runner finishes to undefined behavior.

### Slide 24: Pthreads  -  Runner Function
Refer to the code on Slide 24.

Trace:
- param is argv[1], a string; atoi converts to integer upper.
- sum = 0 initializes shared global.
- Loop computes 1 + 2 + ... + upper.
- pthread_exit(0) terminates the thread.

No race condition here because main does not touch sum until after pthread_join.

### Slide 25: Joining Multiple Threads
Refer to the code on Slide 25.

- Array of thread IDs.
- Join each to wait for all threads.
- Ensures main does not exit before all workers finish.

Exam trap: Joining is not the same as creating. You must join each thread individually.

### Slide 26: Windows Threads  -  Worker Function
Refer to the code on Slide 26.

Trace:
- DWORD = 32-bit unsigned integer.
- WINAPI = calling convention.
- LPVOID = void *.
- Param points to an integer; cast and dereference to get Upper.
- Sum is global, shared.
- Return 0.

### Slide 27: Windows Threads  -  Main
Refer to the code on Slide 27.

Trace:
- CreateThread creates a new thread running Summation with &Param.
- WaitForSingleObject blocks until thread finishes.
- CloseHandle releases thread handle.
- Print Sum.

Trap: Windows uses HANDLE for threads; must close handle to avoid resource leak.

### Slide 28: Java Threads
- Java threads are managed by the JVM.
- Typically implemented using the threads model provided by underlying OS.
- Java threads may be created by:
  1. Extending Thread class
  2. Implementing Runnable interface
- Standard practice is to implement Runnable interface.

Python equivalent: Use threading.Thread with a target function or subclass.

### Slide 29: Java Runnable  -  Python Equivalent
Original Java:
Refer to the code on Slide 29.

Python equivalent (function-based):
Refer to the code on Slide 29.

Python equivalent (subclass-based):
Refer to the code on Slide 29.

Trace:
- threading.Thread creates a thread object.
- start() begins execution.
- join() waits for thread to finish.

### Slide 30: Java Executor  -  Python Equivalent
Original Java:
Refer to the code on Slide 30.

Python equivalent:
Refer to the code on Slide 30.

- ThreadPoolExecutor manages a pool of threads.
- submit schedules the task.
- with block shuts down pool after completion.

### Slide 31: Java Callable  -  Python Equivalent
Original Java:
Refer to the code on Slide 31.

Python equivalent:
Refer to the code on Slide 31.

Trace:
- submit returns a Future.
- future.result() blocks until result is available.
- Returns the computed sum.

### Slide 32: Blank Slide
- This slide is a blank/artifact page in the PDF. No content.

## Mock Paper Code Tracing

### Q16 (Image 2): Fork Only
Refer to the code in the Mock Paper Q16 example.

Trace:
- Global value = 5.
- fork() creates child with a copy of address space; both have value = 5.
- Child: value += 15 to value = 20. Prints VALUE = 20 (Line A).
- Parent: value remains 5. Prints Value = 5 (Line B).
- Parent waits for child.
- Parent prints Value = 5 (Line C).

Correct output:
- Line A: VALUE = 20
- Line B: Value = 5
- Line C: Value = 5

Trap: Many students think parent sees child's change. It does not  -  separate address spaces after fork.

### Q16 (Image 3): Fork + Pthread
Refer to the code in the Mock Paper Q16 example.

Trace:
- Global value = 0.
- fork() creates child with copy of value = 0.
- Child: creates thread runner, which sets value = 5 in the child's address space. pthread_join waits. Prints C: value = 5 (Line C).
- Parent: waits for child to terminate. Parent's value remains 0. Prints P: value = 0 (Line P).

Correct output:
- Line C: C: value = 5
- Line P: P: value = 0

Trap: The thread modifies only the child's memory. Parent's memory is separate due to fork.

### Part 4 Summary

Pthreads: POSIX standard, pthread_create, pthread_join
Windows threads: CreateThread, WaitForSingleObject, CloseHandle
Java/Python threads: Runnable/Thread; Python uses threading.Thread
Executor: Thread pool abstraction; Python ThreadPoolExecutor
Fork + thread: Child gets copy; parent doesn't see child's changes
Fork + global: Separate address spaces; no shared memory

Mnemonics:
- Pthreads: create to join.
- Windows: Create to Wait to Close.
- Python: Thread(target=...) to start() to join().

## Part 5: Implicit Threading (Slides 33-48)

### Slide 33: Implicit Threading  -  Overview
Definition: Creation and management of threads done by compilers and run-time libraries rather than programmers.

Five methods:
1. Thread Pools
2. Fork-Join
3. OpenMP
4. Grand Central Dispatch (GCD)
5. Intel Threading Building Blocks (TBB)

Key idea: Programmer specifies what can run in parallel; the system decides how to map to threads.

### Slide 34: Thread Pools
Definition: Create a number of threads in a pool where they await work.

Advantages:
- Usually slightly faster to service a request with an existing thread than to create a new thread.
- Allows the number of threads in the application(s) to be bound to the size of the pool.
- Separating task from mechanics of creating task allows different strategies.

Windows API supports thread pools:
Refer to the code on Slide 34.

### Slide 35: Java Executors for Thread Pools to Python Equivalent
Original Java:
Refer to the code on Slide 35.

Python equivalent (concurrent.futures):
Refer to the code on Slide 35.

### Slide 36: Java ThreadPoolExample to Python Equivalent
Original Java:
Refer to the code on Slide 36.

Python equivalent:
Refer to the code on Slide 36.

### Slide 37: Fork-Join
Definition: Multiple threads (tasks) are forked, and then joined.

- Fork: split a problem into subtasks that can run in parallel.
- Join: wait for subtasks to complete and combine results.

Mnemonic: Fork = split; Join = merge.

### Slide 38: General Algorithm for Fork-Join
Refer to the code on Slide 38.

### Slide 39: Fork-Join Parallelism (Diagram)
Refer to the diagram on Slide 39.

### Slide 40: Java ForkJoinPool to Python Equivalent
Original Java:
Refer to the code on Slide 40.

Python equivalent (using concurrent.futures.ProcessPoolExecutor):
Refer to the code on Slide 40.

### Slide 41: Java SumTask (RecursiveTask) to Python Equivalent
Original Java:
Refer to the code on Slide 41.

Python equivalent:
Refer to the code on Slide 41.

### Slide 42: ForkJoinTask Class Hierarchy to Python Equivalent
Java hierarchy:
- ForkJoinTask<V> (abstract)
  - RecursiveTask<V> (abstract) to V compute()
  - RecursiveAction (abstract) to void compute()

Python equivalent:
- No direct equivalent; concurrent.futures.Future represents a result.
- Executor.submit() returns a Future.
- Future.result() blocks until result is available.

### Slide 43: OpenMP
- Set of compiler directives and an API for C, C++, FORTRAN.
- Provides support for parallel programming in shared-memory environments.
- Identifies parallel regions  -  blocks of code that can run in parallel.
- #pragma omp parallel to Create as many threads as there are cores.

Refer to the code on Slide 43.

### Slide 44: OpenMP  -  Parallel For
Refer to the code on Slide 44.

### Slide 45: Grand Central Dispatch (GCD)
- Apple technology for macOS and iOS operating systems.
- Extensions to C, C++, and Objective-C languages, API, and run-time library.
- Allows identification of parallel sections.
- Manages most of the details of threading.
- Block is in { }:
Refer to the code on Slide 45.
- Blocks placed in dispatch queue.
- Assigned to available thread in thread pool when removed from queue.

### Slide 46: GCD  -  Dispatch Queues
Two types:
1. Serial  -  blocks removed in FIFO order, queue is per process, called main queue.
2. Concurrent  -  removed in FIFO order but several may be removed at a time.

Four system-wide queues divided by quality of service (QoS):
- QOS_CLASS_USER_INTERACTIVE
- QOS_CLASS_USER_INITIATED
- QOS_CLASS_USER_UTILITY
- QOS_CLASS_USER_BACKGROUND

Exam tip: Serial = one at a time; Concurrent = multiple at a time.

### Slide 47: GCD  -  Swift Language
Refer to the code on Slide 47.

### Slide 48: Intel Threading Building Blocks (TBB)
- Template library for designing parallel C++ programs.
- Serial:
Refer to the code on Slide 48.
- TBB:
Refer to the code on Slide 48.

### Part 5 Summary

Thread Pools: Reuse threads; bound to pool size; faster than creating new
Fork-Join: Divide-and-conquer; fork subtasks, join results
OpenMP: Compiler directives for C/C++/Fortran; #pragma omp parallel
GCD: Apple; blocks in dispatch queues; serial or concurrent
TBB: C++ template library; parallel_for

Mnemonics:
- Pools = reuse.
- Fork-Join = split & merge.
- OpenMP = pragmas.
- GCD = queues & blocks.
- TBB = parallel_for.

Common exam trap: Implicit threading is about what to parallelize, not how to create threads. The system manages threads.

## Part 6: Threading Issues, OS Examples & End of Chapter (Slides 49-63)

### Slide 49: Threading Issues  -  Outline
1. Semantics of fork() and exec() system calls.
2. Signal handling (synchronous and asynchronous).
3. Thread cancellation of target thread (asynchronous or deferred).
4. Thread-local storage (TLS).
5. Scheduler activations.

### Slide 50: fork() and exec() Semantics
Does fork() duplicate only the calling thread or all threads?
- Some UNIX systems have two versions of fork():
  1. Duplicate only the calling thread.
  2. Duplicate all threads.
- exec() usually works as normal  -  it replaces the running process, including all threads.

Trap: exec() always wipes out all threads and replaces the process image. It does not preserve threads.

### Slide 51: Signal Handling  -  Basics
Signal: A notification sent to a process that an event has occurred.

Three steps:
1. Signal is generated by a particular event.
2. Signal is delivered to a process.
3. Signal is handled by one of two handlers:
   - Default handler  -  kernel runs it when handling the signal.
   - User-defined handler  -  can override default.

### Slide 52: Signal Handling  -  Multithreaded
Four options:
1. Deliver the signal to the thread to which the signal applies.
2. Deliver the signal to every thread in the process.
3. Deliver the signal to certain threads in the process.
4. Assign a specific thread to receive all signals for the process.

Trap: Signals are process-wide in single-threaded, but thread-specific in multithreaded. The OS must decide the delivery policy.

### Slide 53: Thread Cancellation
Definition: Terminating a thread before it has finished.

- Target thread: The thread to be canceled.

Two general approaches:
1. Asynchronous cancellation  -  terminates the target thread immediately.
2. Deferred cancellation  -  allows the target thread to periodically check if it should be cancelled.

Pthread code:
Refer to the code on Slide 53.

Trap: Asynchronous cancellation is dangerous  -  the thread may be holding locks or in the middle of updating shared data. Deferred is safer.

### Slide 54: Cancellation State Table

Off. State: Disabled. Type: -
Deferred. State: Enabled. Type: Deferred
Asynchronous. State: Enabled. Type: Asynchronous

Key points:
- If thread has cancellation disabled, cancellation remains pending until thread enables it.
- Default type is deferred.
- Cancellation only occurs when thread reaches a cancellation point (e.g., pthread_testcancel()).
- On Linux systems, thread cancellation is handled through signals.

Exam tip: Memorize the default: Deferred. It's the safe option.

### Slide 55: Java Interrupt to Python Equivalent
Original Java:
Refer to the code on Slide 55.

Python equivalent (using threading.Event):
Refer to the code on Slide 55.

### Slide 56: Thread-Local Storage (TLS)
Definition: TLS allows each thread to have its own copy of data.

Useful when: You do not have control over the thread creation process (e.g., thread pool).

Different from local variables:
- Local variables visible only during single function invocation.
- TLS visible across function invocations.

Similar to static data:
- TLS is unique to each thread.

C example:
Refer to the code on Slide 56.

Python equivalent:
Refer to the code on Slide 56.

Trap: TLS is not the same as local variables or global variables. It's a third category.

### Slide 57: Scheduler Activations
Problem: Both M:M and Two-level models require communication to maintain the appropriate number of kernel threads allocated to the application.

Solution: Use an intermediate data structure  -  Lightweight Process (LWP).

- Appears to be a virtual processor on which process can schedule user thread to run.
- Each LWP attached to kernel thread.
- Scheduler activations provide upcalls  -  a communication mechanism from the kernel to the upcall handler in the thread library.

Diagram:
Refer to the diagram on Slide 57.

Mnemonic: LWP = virtual CPU for user threads.

### Slide 58: OS Examples  -  Outline
1. Windows Threads
2. Linux Threads

### Slide 59: Windows Threads
- Windows API  -  primary API for Windows applications.
- Implements the one-to-one mapping, kernel-level.
- Each thread contains:
  - A thread id.
  - Register set representing state of processor.
  - Separate user and kernel stacks.
  - Private data storage area used by run-time libraries and DLLs.

The register set, stacks, and private storage area are known as the context of the thread.

Trap: Windows uses 1:1 mapping. Each user thread has a corresponding kernel thread.

### Slide 60: Windows Thread Data Structures
1. ETHREAD (Executive Thread Block)  -  includes pointer to process to which thread belongs and to KTHREAD, in kernel space.
2. KTHREAD (Kernel Thread Block)  -  scheduling and synchronization info, kernel-mode stack, pointer to TEB, in kernel space.
3. TEB (Thread Environment Block)  -  thread id, user-mode stack, thread-local storage, in user space.

Mnemonic: E-K-T (Executive, Kernel, Thread Environment). E and K in kernel space; T in user space.

### Slide 61: Windows Threads Data Structures (Diagram)
Refer to the diagram on Slide 61.
- User space: TEB (thread id, user stack, TLS).
- Kernel space: ETHREAD (process pointer, KTHREAD pointer) and KTHREAD (scheduling info, kernel stack, TEB pointer).
- Arrows showing pointers between them.

### Slide 62: Linux Threads
- Linux refers to them as tasks rather than threads.
- Thread creation is done through clone() system call.
- clone() allows a child task to share the address space of the parent task.
- Flags control behavior:

CLONE_FS: File-system information is shared.
CLONE_VM: The same memory space is shared.
CLONE_SIGHAND: Signal handlers are shared.
CLONE_FILES: The set of open files is shared.

- struct task_struct points to process data structures (shared or unique).

Trap: Linux does not distinguish between processes and threads at the kernel level. Both are task_struct. The difference is which flags are passed to clone().

### Slide 63: End of Chapter 4
- Marks the end of Chapter 4.
- Next chapter: CPU Scheduling (Chapter 5).

## Part 6 Summary

fork() / exec(): fork() may duplicate calling thread only; exec() replaces all threads
Signal handling: Four delivery options in multithreaded
Cancellation: Asynchronous (immediate, risky) vs Deferred (safe, default)
TLS: Per-thread global; visible across functions
Scheduler Activations: Upcalls to maintain kernel thread count; uses LWP
Windows Threads: 1:1; ETHREAD, KTHREAD, TEB
Linux Threads: clone() with flags; task_struct

Mnemonics:
- Cancellation: Asynchronous = Abort now; Deferred = Delay until safe.
- Windows: E-K-T (Executive, Kernel, Thread Environment).
- Linux: CLONE_VM = share memory.

Common exam traps:
- Default cancellation is Deferred, not Asynchronous.
- TLS is not equal to local variable is not equal to global variable.
- Windows uses 1:1, not M:M.

## End of Chapter 4 Notes

This completes all slides in Chapter 4: Threads & Concurrency.
