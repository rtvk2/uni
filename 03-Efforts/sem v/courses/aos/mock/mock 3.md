## Mock Quiz 3 — Processes, Threads & CPU Scheduling

**Date:** _______________ **Time: 45 min** **Max Marks: 55**

1. Write _concise, organized_ answers. Verbose or unreadable answers will fetch zero marks.
2. Make _reasonable assumptions_ and _clearly state_ them to answer _ambiguous_ questions. No clarifications will be provided during the exam.

---------------- MCQ Questions (3 marks each): Tick the right option. No explanation. --------------

**1.** Which of the following is **TRUE** about Google Chrome's multiprocess architecture?

A) A single renderer process handles every opened website, to save memory. B) The browser process manages the UI, disk, and network I/O, while a new renderer process is created for each website opened. C) Plug-ins run inside the browser process for tighter integration. D) Renderer processes have unrestricted disk and network I/O access, for performance.

**2.** Which of the following is **FALSE** regarding the motivations for cooperating processes?

A) Cooperating processes may use IPC for information sharing. B) IPC can be used for computation speedup, by having subtasks execute in parallel. C) A process must be a cooperating process in order to be scheduled by the OS at all. D) Modularity is a valid reason to divide system functions across separate cooperating processes.

**3.** Which statement about POSIX shared memory is **TRUE**?

A) `shm_open()` is used to map the shared-memory object into the process's address space. B) `mmap()` memory-maps a shared-memory object (obtained via `shm_open()`), and reads/writes go through the pointer `mmap()` returns. C) `ftruncate()` deletes a shared-memory segment entirely. D) POSIX shared memory does not require an explicit segment size to be set.

**4.** In the bounded-buffer solution using only `in` and `out` (no counter), which condition tells the **producer** that the buffer is full?

A) `in == out` B) `(in + 1) % BUFFER_SIZE == out` C) `out == BUFFER_SIZE - 1` D) `in == BUFFER_SIZE`

**5.** Which statement about Parallelism vs. Concurrency is **TRUE**?

A) Parallelism can be achieved on a single-core processor purely through fast context switching. B) Concurrency requires at least two physical cores to have any meaning. C) A single core can support concurrency (multiple tasks making progress) via scheduling, even though true parallelism requires multiple cores. D) Parallelism and concurrency are interchangeable terms with no meaningful distinction.

**6.** Which implicit-threading approach is defined by "a number of threads created in advance, where they await work," bounding the number of threads an application uses?

A) Fork-Join B) Thread Pool C) OpenMP D) Grand Central Dispatch

**7.** Which statement about `fork()` semantics in a **multithreaded** process is **TRUE**?

A) `fork()` always duplicates every thread of the calling process, with no exceptions across UNIX variants. B) Some UNIX variants provide two versions of `fork()`: one that duplicates all threads, and one that duplicates only the calling thread. C) `exec()` called after `fork()` in a multithreaded process fails to replace the process image. D) `fork()` and `exec()` behave identically in multithreaded and single-threaded processes, with no special-cased issues.

**8.** Thread-Local Storage (TLS) differs from an ordinary local variable in that:

A) TLS is visible only during a single function invocation, exactly like a local variable. B) TLS is visible across multiple function invocations for the _same_ thread (similar to `static` data), but each thread has its own independent copy. C) TLS can be used only by kernel threads, never by user threads. D) TLS eliminates the need for thread pools.

**9.** In Linux load balancing, a _scheduling domain_ groups CPU cores that share resources (e.g., cache). The purpose of organizing domains this way is to:

A) Maximize the frequency of thread migration between domains, for fairness. B) Keep threads from migrating between domains unnecessarily, preserving cache locality. C) Disable NUMA-awareness. D) Force every thread through the O(1) scheduler regardless of CFS.

**10.** In Linux's Completely Fair Scheduler (CFS), the next task chosen to run is the one with:

A) The highest static priority in the real-time class. B) The largest `vruntime` (virtual run time). C) The smallest `vruntime` (virtual run time). D) The longest time remaining in its time slice.

**11.** Which statement about Earliest-Deadline-First (EDF) scheduling is **TRUE**?

A) Priorities are static, assigned once at process creation based on burst time. B) Priorities are assigned dynamically: a task with an earlier deadline gets a higher priority than one with a later deadline. C) EDF is a proportional-share algorithm that allocates a fixed fraction of CPU shares to each task. D) EDF is identical to Rate-Monotonic Scheduling in every scenario.

**12.** A long CPU-bound process runs ahead of several short I/O-bound processes under FCFS, forcing them to wait far longer than their own burst times would suggest. This phenomenon is called:

A) Starvation B) Aging C) Convoy effect D) Priority inversion

---------------- NON MCQ Questions. Give concise explanation. ----------------

**13.** [4] A system uses a 3-level Multilevel Feedback Queue:

- **Q0:** Round Robin, time quantum = 4 ms (highest priority)
- **Q1:** Round Robin, time quantum = 8 ms
- **Q2:** FCFS (lowest priority, runs to completion once dispatched)

A new process P1 enters Q0 needing 18 ms of total CPU time, and is scheduled immediately whenever it (re-)enters a queue (assume no other process is competing for the CPU).

(a) [2] Trace exactly how P1 moves between the three queues and how much CPU time it consumes at each level, until it completes. (b) [2] Explain the purpose of _aging_ (promoting a process back to a higher-priority queue) in an MLFQ, and why a purely demotion-only design (like the one above) risks starving a process that ends up in Q2.

**14.** [5] Four processes arrive for Round Robin scheduling (time quantum = 2 ms) on a single core:

|Process|Arrival Time|Burst Time|
|---|---|---|
|P1|0|5|
|P2|1|4|
|P3|2|2|
|P4|4|1|

(Assume that if a new process arrives at the exact instant a running process's quantum expires, the new arrival is added to the ready queue _before_ the preempted process is placed back at the tail.)

Draw the complete Gantt chart and compute the average waiting time and average turnaround time.

**15.** [5] (a) [3] Contrast the **Many-to-One**, **One-to-One**, and **Many-to-Many** threading models along three dimensions: (i) concurrency achievable on a multicore system, (ii) the effect of one thread making a blocking system call, and (iii) the overhead of creating a new thread. (b) [2] A system observes an average arrival rate of 12 processes/second into the ready queue, and Little's Law gives an average queue length of 3 processes. What is the average waiting time per process? If the arrival rate later doubles to 24/second while the average waiting time stays the same, what is the new average queue length?

**16.** [5] What is the output of the following program?

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    pid_t pid1, pid2;

    pid1 = fork();
    if (pid1 == 0) {
        pid2 = fork();
        if (pid2 == 0) {
            printf("A\n");
        } else {
            wait(NULL);
            printf("B\n");
        }
    } else {
        wait(NULL);
        printf("C\n");
    }
    return 0;
}
```

(a) [1] Draw the process tree (label the original process P0, and its descendants), marking each parent-child relationship. (b) [2] List every letter printed across _all_ processes, and state which process prints it. (c) [1] Is the relative order between "A" and "B" guaranteed? Is the order between "B" and "C" guaranteed? Justify each briefly. (d) [1] How many total processes are created by this program?

---

---

# ANSWER KEY — For Self-Assessment (cover before attempting)

**MCQ**

1. **B**
2. **C** — independent processes can be scheduled perfectly well; cooperation is not a scheduling prerequisite.
3. **B**
4. **B**
5. **C**
6. **B**
7. **B**
8. **B**
9. **B**
10. **C**
11. **B**
12. **C**

**13.** (a) Q0 (q=4): runs 4 ms, 14 ms remain (not finished) → demoted to Q1. Q1 (q=8): runs 8 ms, 6 ms remain → demoted to Q2. Q2 (FCFS, runs to completion): runs the remaining 6 ms and finishes. Total CPU consumed = 4 + 8 + 6 = **18 ms**, matching its total burst; total time in system = 18 ms since nothing else competes for the CPU. (b) Aging protects processes that have become I/O-bound / interactive (or have simply been waiting a long time) from being starved by a constant stream of new arrivals that keep refilling the high-priority queues. In a purely demotion-only design, once a CPU-bound process is pushed down to Q2, it will never be reconsidered for Q0/Q1 — if Q0 and Q1 stay busy indefinitely with new short jobs, the process in Q2 (FCFS) may wait arbitrarily long, i.e., **starve**. An explicit promotion/aging rule (e.g., "move back to Q0 after waiting T ms in a lower queue") is needed to bound this wait.

**14.** Trace: `P1(0-2)` [P2 arrives@1, P3 arrives@2] → queue `[P2,P3,P1(rem3)]` → `P2(2-4)` [P4 arrives@4] → queue `[P3,P1(rem3),P4,P2(rem2)]` → `P3(4-6)` completes (needed exactly 2) → queue `[P1(rem3),P4,P2(rem2)]` → `P1(6-8)`, rem1 → queue `[P4,P2(rem2),P1(rem1)]` → `P4(8-9)` completes (needed only 1) → queue `[P2(rem2),P1(rem1)]` → `P2(9-11)` completes → queue `[P1(rem1)]` → `P1(11-12)` completes.

Full Gantt: `P1(0-2) P2(2-4) P3(4-6) P1(6-8) P4(8-9) P2(9-11) P1(11-12)`.

Completions: P3=6, P4=9, P2=11, P1=12. Turnaround (completion−arrival): P1=12, P2=10, P3=4, P4=5 → **Avg TAT = 31/4 = 7.75**. Waiting (turnaround−burst): P1=7, P2=6, P3=2, P4=4 → **Avg WT = 19/4 = 4.75**.

**15.** (a)

||Many-to-One|One-to-One|Many-to-Many|
|---|---|---|---|
|(i) Parallelism on multicore|None — only one thread can be in the kernel at a time|Full — each user thread has its own kernel thread|Full, up to the number of kernel threads the OS/library provisions|
|(ii) Effect of a blocking syscall|Blocks the _entire process_ (all its threads)|Blocks only the calling thread; others continue|Blocks only the calling thread (another kernel thread can run a different user thread)|
|(iii) Thread-creation overhead|Very low (pure user-space bookkeeping)|Higher — each new user thread requires a new kernel thread|Moderate — kernel threads are reused/pooled, so user threads are cheap to create|

(b) `n = λ × W` → `3 = 12 × W` → **W = 0.25 s**. If λ becomes 24/s and W is unchanged: `n = 24 × 0.25 =` **6 processes**.

**16.** (a) P0 (original) → forks → **P1** (its child); P1 → forks → **P2** (P1's child). So: P0 is P1's parent; P1 is P2's parent. (b) **P2** prints `"A"` (its only action, then it exits). **P1** calls `wait(NULL)` — which blocks until its only child, P2, terminates — and only then prints `"B"`. **P0** calls `wait(NULL)` — which blocks until its only child, P1, terminates — and only then prints `"C"`. So each of A, B, C is printed exactly once, by P2, P1, and P0 respectively. (c) **Both orderings are guaranteed**, which is the twist: P1's `wait(NULL)` forces "A" (printed by P2 right before it exits) to happen **before** P1's own "B". Likewise, P0's `wait(NULL)` forces P1 — and everything P1 already waited on — to fully finish (i.e., "B" already printed) **before** P0 prints "C". So the output is deterministically **A, then B, then C**, every run — unlike an unsynchronized fork tree, the chain of `wait()` calls imposes a strict happens-before ordering across all three processes. (d) **3** total processes (P0, P1, P2).