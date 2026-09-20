## Mock Quiz 1 — Processes, Threads & CPU Scheduling

**Date:** _______________ **Time: 45 min** **Max Marks: 55**

1. Write _concise, organized_ answers. Verbose or unreadable answers will fetch zero marks.
2. Make _reasonable assumptions_ and _clearly state_ them to answer _ambiguous_ questions. No clarifications will be provided during the exam.

---------------- MCQ Questions (3 marks each): Tick the right option. No explanation. --------------

**1.** Which of the following statements about the Process Control Block (PCB) is **FALSE**?

A) The PCB stores the program counter, indicating the next instruction to execute. B) The PCB is also referred to as a task control block. C) The PCB stores the source code (text section) of the program being executed. D) The PCB stores CPU scheduling information such as priority and scheduling-queue pointers.

**2.** In the process state diagram, a process moves from the **Running** state to the **Ready** state as a result of:

A) An I/O or event wait B) An I/O or event completion C) An interrupt (e.g., a timer expiring on a preemptive system) D) Being admitted by the long-term scheduler

**3.** Consider: `pid = fork(); if (pid == 0) execlp("/bin/ls", "ls", NULL);`. Which statement is **TRUE**?

A) `exec()` creates a brand-new process while `fork()` merely replaces the memory image. B) After a successful `exec()`, the child process is assigned a new PID. C) `exec()` replaces the process's memory space with a new program while keeping the same PID. D) `wait()` must be called before `fork()` to avoid a race condition.

**4.** Which of the following statements is **FALSE**?

A) If a parent terminates without calling `wait()`, and its child is still running, the child becomes an _orphan_. B) If a child terminates and the parent has not yet called `wait()`, the child is a _zombie_. C) Cascading termination is initiated by the terminating process's own children calling `exit()`, not by the operating system. D) `exec()` replaces the process's memory image but keeps the same PID.

**5.** Which statement correctly contrasts shared memory and message-passing IPC?

A) Shared memory is generally faster for large amounts of data, since system calls are needed only to establish the shared region, not for every access. B) Message passing requires no OS involvement once the communication link is established. C) Shared memory is easier to use for synchronization since the OS automatically synchronizes access to it. D) Message passing is always faster than shared memory, regardless of data size.

**6.** In the classic bounded-buffer implementation using only `in` and `out` pointers (no counter variable), what is the maximum number of items that can actually be stored at once, out of `BUFFER_SIZE` slots?

A) `BUFFER_SIZE` B) `BUFFER_SIZE − 1` C) `BUFFER_SIZE / 2` D) `BUFFER_SIZE + 1`

**7.** In the Many-to-One threading model, which of the following is **FALSE**?

A) Many user-level threads are mapped to a single kernel thread. B) If one thread makes a blocking system call, the entire process blocks. C) Multiple threads of the same process can run in true parallel on a multicore system. D) Thread management is done efficiently in user space, without kernel intervention for switches between user threads.

**8.** An application is 40% serial and 60% parallel. By Amdahl's Law, as the number of cores _N_ approaches infinity, the theoretical maximum speedup approaches:

A) 0.4 B) 0.6 C) 2.5 D) Infinity

**9.** Which implicit-threading technology uses "blocks" (denoted `^{ }`) placed into a dispatch queue, and is specific to macOS/iOS?

A) OpenMP B) Grand Central Dispatch C) Intel Threading Building Blocks D) Java's Fork-Join framework

**10.** Which statement about thread cancellation is **TRUE**?

A) Asynchronous cancellation allows the target thread to periodically check whether it should terminate. B) Deferred cancellation terminates the target thread immediately, regardless of what it is doing. C) In deferred cancellation, cancellation only takes effect once the target thread reaches a cancellation point, e.g. `pthread_testcancel()`. D) On Linux, thread cancellation is implemented independently of the signal mechanism.

**11.** According to the four situations under which a CPU-scheduling decision may occur, in which of the following is there **no choice** about which process runs next?

A) A process switches from waiting to ready. B) A process switches from running to ready (e.g., due to an interrupt). C) A running process terminates. D) A new process must be picked while others remain in the ready queue.

**12.** Which of the following is **FALSE** regarding dispatch latency?

A) It includes the time to switch context. B) It includes the time to switch to user mode. C) In real-time systems, a high-priority process having to wait for a low-priority process to release needed resources adds to the _conflict phase_ of dispatch latency. D) Dispatch latency is defined as the time from the arrival of an interrupt to the start of the routine that services it.

---------------- NON MCQ Questions. Give concise explanation. ----------------

**13.** [4] Consider the shared-memory bounded-buffer solution that uses a shared `counter` variable (in addition to `in`/`out`). Assume `counter = 5` initially. The following interleaving occurs:

```
Producer: register1 = counter        {register1 = 5}
Producer: register1 = register1 + 1  {register1 = 6}
Consumer: register2 = counter        {register2 = 5}
Consumer: register2 = register2 - 1  {register2 = 4}
Consumer: counter = register2        {counter = ?}
Producer: counter = register1        {counter = ?}
```

(a) [1] What _should_ the value of `counter` be after one produce and one consume, if no race condition occurred? (b) [2] What is the _actual_ final value of `counter` after this specific interleaving? Show the value after each of the two `counter = ...` statements. (c) [1] Name the general term for this class of bug.

**14.** [5] The following processes arrive for CPU scheduling on a single core:

|Process|Arrival Time|Burst Time|
|---|---|---|
|P1|0|7|
|P2|2|4|
|P3|4|1|
|P4|5|4|

(a) [2.5] Draw the Gantt chart for **FCFS** and compute the average waiting time and average turnaround time. (b) [2.5] Draw the Gantt chart for **non-preemptive SJF** and compute the average waiting time and average turnaround time. (Break ties by earlier arrival time.)

**15.** [5] Using the _same_ process table as Q14, draw the Gantt chart for **Shortest-Remaining-Time-First (SRTF)** scheduling. Clearly mark every point in time at which a preemption occurs and name the process that causes it. Compute the average waiting time and average turnaround time.

**16.** [5] What is the output of the following program? (Assume `fork()` always succeeds.)

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    int x = 1;

    fork();
    x++;

    fork();
    x++;

    printf("x = %d\n", x);
    return 0;
}
```

(a) [1] How many total processes execute this program, including the original? (b) [3] What value(s) of `x` get printed, and how many times is each value printed? Justify by tracing how `x` evolves along every branch. (c) [1] Is the exact order in which the lines are printed to the terminal guaranteed? Justify briefly.

---

---

# ANSWER KEY — For Self-Assessment (cover before attempting)

**MCQ**

1. **C** — the PCB stores state/PC/registers/scheduling/memory/accounting/I-O info, never the program's own code.
2. **C** — Running→Ready happens only via an interrupt (preemption); I/O events involve the Waiting state instead.
3. **C** — `exec()` overlays the _same_ process (same PID) with a new program image.
4. **C** — cascading termination is initiated by the **OS**, not by the children themselves.
5. **A** — shared memory needs system calls only to set up the region; every message-passing `send`/`receive` typically needs OS/system-call involvement.
6. **B** — the pointer-only scheme wastes one slot to distinguish "full" from "empty".
7. **C** — Many-to-One cannot achieve parallelism: only one thread can be in the kernel at a time.
8. **C** — limit = 1/S = 1/0.4 = 2.5.
9. **B** — Grand Central Dispatch.
10. **C** — deferred cancellation only fires at a cancellation point.
11. **C** — termination (and blocking) give no scheduling choice; preemption points (2) and (3) do.
12. **D** — that is the definition of _interrupt_ latency, not dispatch latency.

**13.** (a) Net effect of one produce + one consume = 0 change → `counter` should still be **5**. (b) Consumer stores first: `counter = register2 = 4`. Producer stores next (overwriting): `counter = register1 = 6`. Final value = **6** (one increment "gained" incorrectly — the mirror image of the classic lost-update bug). (c) **Race condition.**

**14.** (a) **FCFS:** Gantt: `P1(0-7) P2(7-11) P3(11-12) P4(12-16)`. Waiting: P1=0, P2=5, P3=7, P4=7 → **Avg WT = 19/4 = 4.75**. Turnaround: 7, 9, 8, 11 → **Avg TAT = 35/4 = 8.75**.

(b) **Non-preemptive SJF:** P1 must run first (0-7, nothing else has arrived/nothing preempts). At t=7 ready = {P2(4), P3(1), P4(4)} → run P3 (7-8). At t=8 ready = {P2(4), P4(4)}, tie broken by arrival → P2 (8-12), then P4 (12-16). Waiting: P1=0, P3=3, P2=6, P4=7 → **Avg WT = 16/4 = 4.0**. Turnaround: 7, 4, 10, 11 → **Avg TAT = 32/4 = 8.0**. (SJF beats FCFS on average waiting time here, as expected.)

**15. SRTF:** Gantt: `P1(0-2) P2(2-4) P3(4-5) P2(5-7) P4(7-11) P1(11-16)`.

- Preemption at t=2: P2 (remaining 4) arrives and is shorter than P1's remaining 5 → preempts P1.
- Preemption at t=4: P3 (remaining 1) arrives and is shorter than P2's remaining 2 → preempts P2.
- At t=5, P3 finishes and P4 arrives; shortest remaining among {P1:5, P2:2, P4:4} is P2 → resumes P2.
- At t=7, P2 finishes; shortest remaining among {P1:5, P4:4} is P4 → runs P4.
- At t=11, P4 finishes; only P1 left → runs to completion at t=16.

Completions: P3=5, P2=7, P4=11, P1=16. Turnaround: P1=16, P2=5, P3=1, P4=6 → **Avg TAT = 28/4 = 7.0**. Waiting (TAT − burst): P1=9, P2=1, P3=0, P4=2 → **Avg WT = 12/4 = 3.0**.

**16.** (a) Every `fork()` call here is **unconditional** (no `if (pid==0)` branching), so each of the 2 forks doubles the process count: **4 total processes**. (b) Every process executes the identical code path: start `x=1` → (fork #1) → `x++` → `x=2` in **both** resulting processes → (fork #2, each of the 2 processes forks again) → `x++` → `x=3` in **all 4** processes. So **`x = 3` is printed 4 times**, and no other value is ever printed — the unconditional forking means every copy performs exactly the same two `x++` increments regardless of which branch it is. (c) **No.** `fork()` gives no ordering guarantee between parent and child (or among unrelated sibling processes); the actual interleaving of the 4 processes' `printf` calls depends on the OS scheduler, so any of the 4! orderings (with all lines reading "x = 3") is possible.