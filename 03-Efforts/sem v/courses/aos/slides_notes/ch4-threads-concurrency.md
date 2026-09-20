---
title: "OS Ch4 — Threads & Concurrency"
course: Operating Systems
chapter: 4
tags: [os, exam-prep, threads, concurrency]
---

## Motivation
- Most modern apps are multithreaded; threads run **within** a process.
- Multiple tasks within one app → separate threads (e.g. update display, fetch data, spellcheck, answer network request).
- Process creation = heavy-weight; thread creation = light-weight.
- Simplifies code, increases efficiency. Kernels themselves generally multithreaded.

## Benefits of Multithreading — **RSEE**
- **R**esponsiveness — app keeps running even if part is blocked (esp. UI).
- **S**haring of resources — threads share process resources; easier than shared memory/message passing between processes.
- **E**conomy — cheaper than process creation; thread switch overhead < context switch.
- **S**calability — process exploits multicore architectures.

> [!note]- Exam angle
> "List/explain the benefits of multithreading" — give all 4 with one-line justification each.

## Multicore Programming
- Challenges for programmers: **dividing activities, balance, data splitting, data dependency, testing & debugging**.
- **Parallelism**: system performs >1 task simultaneously (needs multiple cores).
- **Concurrency**: >1 task making progress (possible even on single core via scheduler — tasks interleave, not simultaneous).
  - Single-core/processor: scheduler *provides* concurrency (not parallelism).

> [!note]- Exam angle: Concurrency vs Parallelism
> Concurrency = single-core interleaving (possible without multiple cores). Parallelism = true simultaneous execution, requires multiple cores. Classic comparison question.

### Types of Parallelism
- **Data parallelism** — same operation applied to subsets of the same data, distributed across cores.
- **Task parallelism** — distributing *threads* (not data) across cores; each thread does a unique operation.

### Amdahl's Law
- Predicts speedup from adding cores to an app with serial (S) + parallel components.
- **Speedup ≤ 1 / (S + (1−S)/N)**, N = # processing cores.
- Example: app 75% parallel / 25% serial (S=0.25). Going 1→2 cores → speedup = 1.6×.
- As N→∞, speedup → **1/S**. ⇒ Serial portion has *disproportionate* effect on achievable speedup.
- Caveat raised in slides: does the law account for contemporary multicore systems? (open question, likely conceptual/discussion point.)

> [!note]- Exam angle: numeric Amdahl's Law problem
> Given %parallel/%serial and N cores, compute speedup using the formula. Also expect "as N→∞" limit question (answer: 1/S).

## User Threads vs Kernel Threads
- **User threads** — managed by a user-level thread library, kernel unaware. Libraries: **POSIX Pthreads, Windows threads, Java threads**.
- **Kernel threads** — supported/managed directly by the kernel. Present in virtually all general-purpose OSes: Windows, Linux, macOS, iOS, Android.

## Multithreading Models (map user ↔ kernel threads)
| Model | Mapping | Notes | Examples |
|---|---|---|---|
| **Many-to-One** | many user threads → 1 kernel thread | One thread blocking blocks **all**; no true parallelism on multicore (only 1 in kernel at a time); rarely used | Solaris Green Threads, GNU Portable Threads |
| **One-to-One** | each user thread → its own kernel thread | More concurrency than M:1; # threads/process sometimes capped due to overhead | Windows, Linux |
| **Many-to-Many** | many user threads → many (≤ or =) kernel threads | OS can create sufficient kernel threads; not very common otherwise | Windows ThreadFiber package |
| **Two-level** | like M:M but also allows binding a user thread to one kernel thread | hybrid of M:M | — |

> [!note]- Exam angle
> Classic "compare the three/four multithreading models" — table above is the whole answer. Know the blocking problem of Many-to-One specifically.

## Thread Libraries
- Provides programmer API to create/manage threads.
- Two implementation styles: **entirely in user space**, or **kernel-level library supported by OS**.

### Pthreads
- POSIX standard **IEEE 1003.1c**; may be user-level or kernel-level.
- It's a **specification, not an implementation** — behavior defined, library implementation is up to developers.
- Common in Linux, macOS.

### Java Threads
- Managed by the JVM; typically implemented via underlying OS's thread model.
- Created by: **extending Thread class** OR **implementing Runnable interface** (standard/preferred practice = implement Runnable).
- **Java Executor Framework** — alternative to explicit thread creation; uses `Executor` interface.

> [!note]- Exam angle
> "Two ways to create a Java thread" — extend Thread vs implement Runnable; know Runnable is preferred.

## Implicit Threading
- Thread creation/management done by **compilers & run-time libraries**, not the programmer — growing in popularity as thread counts grow (explicit threading correctness gets hard).
- **Five methods**: Thread Pools, Fork-Join, OpenMP, Grand Central Dispatch, Intel TBB.

### Thread Pools
- Pre-create a pool of threads that await work.
- Advantages: (1) servicing request with existing thread is faster than creating new one; (2) bounds # threads in app to pool size; (3) separates task definition from task-creation mechanics → allows different run strategies (e.g. periodic scheduling).
- Java: `Executors` class has **three factory methods** for creating pools.

### Fork-Join
- Multiple threads (tasks) are **forked**, then **joined**.
- Java: `ForkJoinTask` = abstract base class.
  - `RecursiveTask` → **returns a result** (via `compute()` return value).
  - `RecursiveAction` → **no result returned**.

> [!note]- Exam angle
> RecursiveTask vs RecursiveAction — returns value vs doesn't. Easy 1-mark distinction question.

### OpenMP
- Set of **compiler directives + API** for C, C++, FORTRAN.
- Support for parallel programming in **shared-memory** environments.
- Identifies **parallel regions** — code blocks that can run in parallel.
- `#pragma omp parallel` → creates as many threads as there are cores.
- Can also parallelize a `for` loop directly.

### Grand Central Dispatch (GCD)
- Apple technology (macOS, iOS). Extensions to C/C++/Objective-C + API + runtime library.
- Identifies parallel sections, manages most threading detail.
- **Block**: `^{ printf("I am a block"); }` — blocks placed in a **dispatch queue**; assigned to an available thread pool thread when removed from queue.
- **Two dispatch queue types**:
  - **Serial** — FIFO removal, one **per process**, called the **main queue**; programmer can create additional serial queues.
  - **Concurrent** — FIFO removal but several removed at once; **4 system-wide queues** by quality of service: `QOS_CLASS_USER_INTERACTIVE`, `QOS_CLASS_USER_INITIATED`, `QOS_CLASS_USER_UTILITY`, `QOS_CLASS_USER_BACKGROUND`.
- Swift: task = a **closure** (like a block, minus the caret `^`); submitted via `dispatch_async()`.

> [!note]- Exam angle
> Serial vs concurrent dispatch queue — serial is per-process/main queue; concurrent has the 4 QoS system queues. Know the QoS class names.

### Intel Threading Building Blocks (TBB)
- Template library for designing **parallel C++** programs.
- Converts a serial `for` loop into a parallel one using `parallel_for`.

## Threading Issues

### fork() and exec() semantics
- Open question: does `fork()` duplicate **only the calling thread** or **all threads**? (Some UNIXes provide two versions of fork for this.)
- `exec()` works as normal — replaces the entire running process **including all threads**.

### Signal Handling
- Signals notify a process an event occurred; handled by a **signal handler**.
- Sequence: (1) signal generated by an event → (2) delivered to a process → (3) handled by **default** or **user-defined** handler.
- Every signal has a kernel default handler; user-defined handler can override it.
- Single-threaded: signal delivered straightforwardly to the process.
- **Multithreaded — where should signal go?** (4 options, all valid design choices):
  1. Deliver to the thread the signal applies to.
  2. Deliver to every thread in the process.
  3. Deliver to certain (specific) threads in the process.
  4. Assign one specific thread to receive all signals for the process.

> [!note]- Exam angle
> "List the options for delivering a signal in a multithreaded process" — expect all 4 verbatim.

### Thread Cancellation
- Terminating a thread before it finishes; thread being cancelled = **target thread**.
- Two approaches:
  - **Asynchronous cancellation** — terminates target thread **immediately**.
  - **Deferred cancellation** — target thread **periodically checks** whether it should cancel (default type in Pthreads).
- Cancellation is only a *request* — actual cancellation depends on thread state.
- If cancellation is disabled on a thread, request stays **pending** until thread re-enables it.
- Deferred: cancellation occurs only at a **cancellation point** (e.g. `pthread_testcancel()`) → then a **cleanup handler** runs.
- On Linux, thread cancellation is implemented via **signals**.
- **Java**: deferred cancellation via `interrupt()` method — sets the thread's interrupted status; thread checks via `isInterrupted()`.

> [!note]- Exam angle
> Asynchronous vs deferred cancellation — deferred is the default and safer (uses cancellation points); know pthread_testcancel() and Java's interrupt().

### Thread-Local Storage (TLS)
- Lets each thread keep its **own copy** of data.
- Useful when you don't control thread creation (e.g. **thread pools**).
- **TLS vs local variables**: local vars visible only during a single function invocation; TLS visible **across** function invocations (persists).
- **TLS vs static data**: similar (persists), but TLS is **unique per thread** (static is shared across threads).

> [!note]- Exam angle
> Compare TLS to (a) local variables and (b) static/global data — exact two-part comparison often asked.

### Scheduler Activations
- Both **M:M** and **Two-level** models need kernel↔user-library communication to maintain the right number of kernel threads.
- Intermediate data structure: **Lightweight Process (LWP)** — appears as a virtual processor the process can schedule a user thread onto; each LWP attached to one kernel thread.
- **Scheduler activations** = mechanism providing **upcalls**: kernel → upcall handler in the thread library, so the app can maintain the correct number of kernel threads.

## OS Examples

### Windows Threads
- Windows API is primary API; implements **one-to-one** mapping, kernel-level.
- Each thread has: thread id, register set (processor state), separate **user & kernel stacks**, private data storage (for run-time libs/DLLs).
- Register set + stacks + private storage area = the thread's **context**.
- Primary data structures:
  - **ETHREAD** (executive thread block) — pointer to process + to KTHREAD; **kernel space**.
  - **KTHREAD** (kernel thread block) — scheduling/sync info, kernel-mode stack, pointer to TEB; **kernel space**.
  - **TEB** (thread environment block) — thread id, user-mode stack, TLS; **user space**.

> [!note]- Exam angle
> ETHREAD/KTHREAD = kernel space; TEB = user space. Common "which structure holds X" question.

### Linux Threads
- Linux calls them **tasks**, not threads.
- Created via **`clone()`** system call.
- `clone()` lets a child task **share the address space** of the parent task (process); flags control exactly what's shared.
- `struct task_struct` points to process data structures (may be shared or unique per task).
