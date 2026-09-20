## Mock Quiz 2 — Processes, Threads & CPU Scheduling

**Date:** _______________ **Time: 45 min** **Max Marks: 55**

1. Write _concise, organized_ answers. Verbose or unreadable answers will fetch zero marks.
2. Make _reasonable assumptions_ and _clearly state_ them to answer _ambiguous_ questions. No clarifications will be provided during the exam.

---------------- MCQ Questions (3 marks each): Tick the right option. No explanation. --------------

**1.** Which of the following is **TRUE** about ordinary (unnamed) UNIX pipes?

A) They allow bidirectional communication between any two unrelated processes. B) They require a parent-child (or common-ancestor) relationship between the communicating processes, and are unidirectional. C) They can be accessed by any process that knows their name, even without a common ancestor. D) They exist only on Windows, not on UNIX.

**2.** Which of the following is **FALSE** about named pipes (FIFOs)?

A) Communication is bidirectional. B) No parent-child relationship is required between communicating processes. C) Several unrelated processes can use the same named pipe. D) Named pipes exist only on UNIX, not on Windows.

**3.** In _direct_ communication (message passing), which statement is **TRUE**?

A) A single link may be associated with more than one pair of processes. B) Between each pair of communicating processes, there exists exactly one link. C) Processes communicate through a shared mailbox identified by a unique ID. D) The link's capacity is always unbounded.

**4.** P1, P2, and P3 all share mailbox A. P1 sends a message, and both P2 and P3 issue a `receive()` on A. Which of the following is **NOT** one of the standard solutions to the resulting "who gets the message?" ambiguity?

A) Allow a link to be associated with at most two processes. B) Allow only one process at a time to execute a receive on the mailbox. C) Let the system arbitrarily pick the receiver, and notify the sender who received it. D) Automatically duplicate the message and deliver a full copy to every process sharing the mailbox.

**5.** Which threading model allows a user thread to be explicitly **bound** to a kernel thread while other threads still use a many-to-many mapping?

A) Many-to-One B) One-to-One C) Two-level model D) Many-to-Many

**6.** Which of the following is a genuine drawback of the **One-to-One** threading model (used by Windows and Linux)?

A) One thread blocking causes the entire process to block. B) The number of threads per process is sometimes restricted, because each user thread requires a corresponding kernel thread. C) Threads cannot run in parallel on a multicore system. D) It offers less concurrency than the Many-to-One model.

**7.** Which statement about Java's Fork-Join framework is **TRUE**?

A) `RecursiveTask`'s `compute()` method cannot return a value. B) `RecursiveAction` is used when a result must be returned from `compute()`. C) `ForkJoinTask` is a concrete class instantiated directly by application code. D) `RecursiveTask` extends `ForkJoinTask` and returns a result via `compute()`.

**8.** Which pragma correctly tells an OpenMP-enabled C compiler to run the iterations of the following `for` loop in parallel across the available cores?

A) `#pragma omp fork` B) `#pragma omp parallel for` C) `dispatch_async(queue, block)` D) `ForkJoinPool.invoke(task)`

**9.** Which of the following statements about Thread Scheduling (PCS vs. SCS) is **FALSE**?

A) Process-Contention Scope (PCS) competition occurs among threads belonging to the same process. B) System-Contention Scope (SCS) competition occurs among all threads in the system. C) `PTHREAD_SCOPE_PROCESS` requests SCS scheduling. D) Linux and macOS support only `PTHREAD_SCOPE_SYSTEM`.

**10.** Which statement about processor affinity is **TRUE**?

A) Hard affinity is merely a preference the OS tries to honor but does not guarantee. B) Soft affinity lets a process explicitly specify the exact set of processors it must run on. C) Load balancing can work against processor affinity, since migrating a thread to a less-loaded CPU discards the cache "warmth" it built up on its original CPU. D) NUMA-aware scheduling ignores memory locality relative to the CPU a thread runs on.

**11.** In Rate-Monotonic scheduling, a periodic process with a **shorter period** is assigned:

A) A lower priority than a process with a longer period. B) A higher priority than a process with a longer period. C) The same priority as every other periodic process. D) A priority unrelated to its period.

**12.** By Little's Formula (n = λ × W), if the average arrival rate into a queue is 5 processes/second and the average number of processes waiting in the queue is 15, the average waiting time W is:

A) 75 seconds B) 3 seconds C) 0.33 seconds D) 20 seconds

---------------- NON MCQ Questions. Give concise explanation. ----------------

**13.** [4] An application spends 20% of its execution time in code that must run serially; the remaining 80% is perfectly parallelizable.

(a) [2] Using Amdahl's Law, calculate the theoretical speedup on a system with **8** processing cores. Show your work. (b) [2] As the number of cores _N_ approaches infinity, what upper bound does the speedup approach? What does this imply about optimizing the serial portion of code versus simply adding more cores?

**14.** [5] Four processes arrive at t = 0 for scheduling on a single core:

|Process|Burst Time|Priority (1 = highest)|
|---|---|---|
|P1|5|2|
|P2|3|1|
|P3|6|2|
|P4|2|3|

The scheduler always runs the highest-priority _ready_ process; among processes of **equal** priority, it uses Round Robin with time quantum = 2. Once a process begins running, it is not preempted by a lower- or equal-priority arrival (there are no further arrivals after t = 0 anyway).

Draw the complete Gantt chart and compute the average waiting time and average turnaround time.

**15.** [5] Draw the complete process-state diagram (states: **New, Ready, Running, Waiting, Terminated**), labeling every transition arrow with the specific triggering event. Then answer:

(a) [1] Which single transition occurs when the long-term scheduler admits a process? (b) [1] A running process issues a blocking I/O request. Name the two states involved and the direction of the transition. (c) [1] Which transition specifically represents preemption due to a timer interrupt? (d) [2] List **all six** labeled transitions that appear on the diagram.

**16.** [5] Consider the following (intentionally unsynchronized) Pthreads program:

```c
#include <pthread.h>
#include <stdio.h>

int counter = 0;

void *increment(void *param) {
    int i;
    for (i = 0; i < 1000000; i++)
        counter++;     /* not protected by a mutex */
    pthread_exit(0);
}

int main() {
    pthread_t t1, t2;
    pthread_create(&t1, NULL, increment, NULL);
    pthread_create(&t2, NULL, increment, NULL);
    pthread_join(t1, NULL);
    pthread_join(t2, NULL);
    printf("counter = %d\n", counter);
    return 0;
}
```

(a) [1] What value _should_ `counter` hold if the increments were properly synchronized? (b) [2] Break `counter++` into its load/increment/store steps and explain, using a concrete interleaving of the two threads, why the actual printed value is frequently **less** than the expected value. (c) [1] Name the general term for two threads unintentionally accessing shared data at the same time without synchronization. (d) [1] Would declaring `counter` as `volatile` fix this problem? Briefly justify.

---

---

# ANSWER KEY — For Self-Assessment (cover before attempting)

**MCQ**

1. **B**
2. **D** — named pipes are supported on _both_ UNIX and Windows.
3. **B**
4. **D** — this is not one of the three listed solutions; the standard solutions restrict/arbitrate the receiver rather than duplicating the message.
5. **C** — the Two-level model.
6. **B**
7. **D**
8. **B**
9. **C** — `PTHREAD_SCOPE_PROCESS` actually requests **PCS**, not SCS (the statement swaps them).
10. **C**
11. **B**
12. **B** — 15 = 5 × W → W = 3 s.

**13.** (a) Speedup = 1 / (S + (1−S)/N) = 1 / (0.2 + 0.8/8) = 1 / (0.2 + 0.1) = 1 / 0.3 ≈ **3.33×**. (b) Limit = 1/S = 1/0.2 = **5×**. Implication: no matter how many cores are added, speedup can never exceed 5×; beyond a certain point, reducing the serial fraction S gives far more benefit than adding cores (diminishing returns on parallel hardware once S dominates).

**14.** Priorities: P2 (1, highest) runs alone and completely first: `P2(0-3)`. Then priority-2 tier {P1(5), P3(6)} runs RR q=2: `P1(3-5) P3(5-7) P1(7-9) P3(9-11) P1(11-12) P3(12-14)` (P1 finishes its last 1 ms unit at t=12; P3 finishes its last 2 ms at t=14). Finally P4 (priority 3, lowest): `P4(14-16)`.

Full Gantt: `P2(0-3) P1(3-5) P3(5-7) P1(7-9) P3(9-11) P1(11-12) P3(12-14) P4(14-16)`.

Completions: P2=3, P1=12, P3=14, P4=16. Waiting (completion − burst, all arrived at 0): P2=0, P1=7, P3=8, P4=14 → **Avg WT = 29/4 = 7.25**. Turnaround (= completion, since arrival=0): 3, 12, 14, 16 → **Avg TAT = 45/4 = 11.25**.

**15.** Six transitions: **New→Ready** (admitted), **Ready→Running** (scheduler dispatch), **Running→Ready** (interrupt), **Running→Waiting** (I/O or event wait), **Waiting→Ready** (I/O or event completion), **Running→Terminated** (exit). (a) New → Ready. (b) Running → Waiting. (c) Running → Ready. (d) As listed above.

**16.** (a) **2,000,000** (1,000,000 increments from each of the two threads). (b) `counter++` expands to `register = counter; register = register + 1; counter = register`. If Thread A executes `register_A = counter` (reading, say, 500) and, before it stores back, Thread B also executes `register_B = counter` (also reading 500), then both threads independently compute 501 and store 501 back — one of the two increments is **lost**, because the second store simply overwrites the first with the same value instead of building on it. Repeated millions of times, this produces a final count noticeably below 2,000,000 (and the exact shortfall varies run to run). (c) **Race condition.** (d) **No.** `volatile` only forces the compiler to re-read/re-write the variable from memory each time (preventing register caching across iterations) and prevents certain compiler reorderings — it does **not** make the read-modify-write sequence atomic. Two threads can still interleave between the load and the store, so the lost-update race condition persists. A mutex (or an atomic increment) is required to actually fix it.