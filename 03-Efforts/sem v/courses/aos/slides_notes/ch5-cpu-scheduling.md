---
title: "OS Ch5 — CPU Scheduling"
course: Operating Systems
chapter: 5
tags: [os, exam-prep, scheduling]
---

## Basic Concepts
- Max **CPU utilization** obtained via multiprogramming.
- **CPU–I/O burst cycle**: process execution = alternating CPU burst → I/O burst.
- Burst-time histogram: **large number of short bursts, small number of long bursts** (CPU-bound vs I/O-bound processes).

## CPU Scheduler
- Selects a process from the **ready queue**, allocates a CPU core to it. Queue may be ordered various ways.
- Scheduling decisions occur when a process:
  1. Switches **running → waiting**
  2. Switches **running → ready**
  3. Switches **waiting → ready**
  4. **Terminates**
- Cases **1 & 4**: no choice — must pick a new process if one exists (this is what makes nonpreemptive scheduling well-defined).
- Cases **2 & 3**: there **is** a choice (this is where preemption decisions happen).

## Preemptive vs Nonpreemptive
- **Nonpreemptive**: scheduling decisions only at 1 & 4. Once CPU allocated, process keeps it until it terminates or switches to waiting.
- **Preemptive**: scheduling can also happen at 2 & 3.
- Virtually all modern OSes (Windows, macOS, Linux, UNIX) use **preemptive** scheduling.
- **Preemptive scheduling can cause race conditions** when data is shared between processes — preempt mid-update, second process reads inconsistent state. (Detailed in Ch6.)

## Dispatcher
- Module that gives the selected process control of the CPU: switches context, switches to user mode, jumps to proper location to resume the program.
- **Dispatch latency** = time for dispatcher to stop one process and start another.

## Scheduling Criteria (and optimization direction)
| Criterion | Optimize |
|---|---|
| CPU utilization | **Max** |
| Throughput (# processes completed / time unit) | **Max** |
| Turnaround time (total time to execute a process) | **Min** |
| Waiting time (time spent waiting in ready queue) | **Min** |
| Response time (time from request submission → first response) | **Min** |

> [!note]- Exam angle
> Define each criterion precisely AND know which direction to optimize — frequently tested together.

## Scheduling Algorithms

### FCFS (First-Come, First-Served)
- Nonpreemptive by nature; simple.
- **Convoy effect**: short processes queue behind one long process (bad if one CPU-bound + many I/O-bound processes) → low CPU/device utilization.
- Example (P1=24, P2=3, P3=3):
  - Order P1,P2,P3 → wait: 0, 24, 27 → **avg = 17**
  - Order P2,P3,P1 → wait: 6, 0, 3 → **avg = 3** (much better — order/arrival matters a lot)

> [!note]- Exam angle: numeric
> Classic Gantt-chart + average-waiting-time computation. Always sensitive to arrival order for FCFS — this is the point of the two-order example.

### SJF (Shortest-Job-First)
- Schedule the process with shortest **next CPU burst**.
- **SJF is optimal** — minimum average waiting time for a given process set.
- Preemptive version = **Shortest-Remaining-Time-First (SRTF)**.
- Burst length is only estimated — via user input or **exponential averaging**.
- Example (P1=6,P2=8,P3=7,P4=3), order run: P4,P1,P3,P2 → **avg wait = (3+16+9+0)/4 = 7**

#### Exponential Averaging (burst prediction)
- Formula: **τₙ₊₁ = α·tₙ + (1−α)·τₙ**  (tₙ = actual last burst, τₙ = previous prediction)
- α commonly = **½**.
- **α = 0** → τₙ₊₁ = τₙ (recent history ignored, prediction never changes).
- **α = 1** → τₙ₊₁ = tₙ (only the most recent actual burst counts).
- Expanded: τₙ₊₁ = α·tₙ + (1−α)α·tₙ₋₁ + … + (1−α)ʲα·tₙ₋ⱼ + … + (1−α)ⁿ⁺¹τ₀ — each older term has **exponentially decreasing weight** (since α, (1−α) ≤ 1).

> [!note]- Exam angle
> Numeric exponential-averaging problems (given α, previous τ, sequence of bursts, compute next prediction) are very common — also the α=0/α=1 edge-case conceptual question.

### SRT (Shortest-Remaining-Time-First)
- Preemptive SJF: whenever a new process arrives, redo the SJF decision including the new arrival.
- Example: P1(arr 0, burst 8), P2(arr 1, burst 4), P3(arr 2, burst 9), P4(arr 3, burst 5)
  - Gantt: P1[0–1], P2[1–5], P4[5–10], P1[10–17], P3[17–26]
  - **Avg wait = [(10−1)+(1−1)+(17−2)+(5−3)]/4 = 26/4 = 6.5**

> [!note]- Exam angle
> This is the standard "preemptive SJF with staggered arrivals" numeric problem — practice building the Gantt chart by re-evaluating remaining time at every new arrival.

### Round Robin (RR)
- Each process gets a **time quantum q** (typically 10–100 ms); preempted after q, sent to back of ready queue.
- n processes, quantum q → each process gets **1/n** of CPU in chunks of ≤ q; **no process waits more than (n−1)q**.
- Timer interrupts every quantum to trigger reschedule.
- **q large → behaves like FCFS**; **q small → true RR** (but context-switch overhead rises).
- **q must be large relative to context-switch time**, else overhead dominates (context switch < 10 μs typically; q = 10–100 ms).
- Rule of thumb: **80% of CPU bursts should be shorter than q**.
- Example (P1=24,P2=3,P3=3, q=4): Gantt P1[0-4] P2[4-7] P3[7-10] P1[10-14][14-18][18-22][22-26][26-30].
  - Typically higher avg **turnaround** than SJF, but **better response time**.

> [!note]- Exam angle
> q→∞ behaves like FCFS; q→0 maximizes responsiveness but maximizes overhead. Always mention the context-switch-overhead tradeoff.

### Priority Scheduling
- Integer priority per process; CPU → **highest priority** (convention: **smaller integer = higher priority**).
- Can be preemptive or nonpreemptive.
- **SJF = priority scheduling** where priority = inverse of predicted next CPU burst.
- **Problem: Starvation** — low-priority processes may never run.
- **Solution: Aging** — gradually increase priority of waiting processes over time.
- Example table (P1–P5, burst & priority) → **avg wait = 8.2**.
- **Priority + Round Robin**: run highest priority first; same-priority processes run round robin among themselves.

> [!note]- Exam angle
> Starvation→Aging is a guaranteed short-answer pairing. Also: "does smaller or larger number = higher priority?" (smaller, per these slides' convention — but always check problem statement, conventions vary).

### Multilevel Queue
- Ready queue split into **multiple separate queues** (e.g. foreground/background).
- Defined by: (1) number of queues, (2) scheduling algorithm per queue, (3) method to assign a process to a queue, (4) scheduling **among** the queues.
- E.g. one queue per priority level → always schedule from the highest-priority nonempty queue.
- Can also prioritize by **process type** (interactive, batch, etc).
- **A process stays in its assigned queue permanently** (this is the key difference from MLFQ below).

### Multilevel Feedback Queue (MLFQ)
- Like multilevel queue, but a process **can move between queues**.
- Defined by: (1) # queues, (2) algorithm per queue, (3) method to **upgrade** a process, (4) method to **demote** a process, (5) method to assign an entering process to a queue.
- **Aging is implemented via MLFQ** (demoted for using too much CPU, promoted for waiting too long).
- Example: Q0 = RR(q=8ms), Q1 = RR(q=16ms), Q2 = FCFS.
  - New process enters Q0. Gets 8ms; if unfinished → demoted to Q1.
  - In Q1 gets 16ms more; if still unfinished → demoted to Q2 (FCFS, run to completion or until preempted by arrival).

> [!note]- Exam angle
> Multilevel Queue (fixed assignment) vs MLFQ (process moves between queues, implements aging) — key distinguishing exam question. Also expect a "trace the MLFQ example" numeric question.

## Thread Scheduling
- Distinction: user-level vs kernel-level threads.
- When threads are supported, **threads** are scheduled, not processes.
- M:1 and M:M models: thread library schedules user threads onto an **LWP** → this competition is **Process-Contention Scope (PCS)** — competition **within** the process, priority typically set by the programmer.
- Kernel thread scheduled onto a CPU → **System-Contention Scope (SCS)** — competition among **all threads system-wide**.

### Pthread Scheduling API
- API lets you specify PCS or SCS at thread creation:
  - `PTHREAD_SCOPE_PROCESS` → PCS
  - `PTHREAD_SCOPE_SYSTEM` → SCS
- **Linux and macOS only support `PTHREAD_SCOPE_SYSTEM`** (SCS) — OS can restrict this.
- Functions: `pthread_attr_getscope()`, `pthread_attr_setscope()`.

> [!note]- Exam angle
> PCS vs SCS mapping to PTHREAD_SCOPE_PROCESS/SYSTEM, and the Linux/macOS restriction to SCS-only, is a common recall question.

## Multiple-Processor Scheduling
- More complex with multiple CPUs. Architectures: **multicore CPUs, multithreaded cores, NUMA systems, heterogeneous multiprocessing**.
- **SMP (Symmetric Multiprocessing)**: each processor is self-scheduling.
  - (a) all threads in one **common ready queue**, or
  - (b) each processor has its **own private queue**.

### Multicore Processors
- Multiple cores on one chip → faster, less power.
- Multiple hardware threads per core also increasingly common: exploits **memory stall** — switch to another thread while one waits on memory.
- **Chip Multithreading (CMT)** — each core has multiple hardware threads (Intel calls this **hyperthreading**).
  - E.g. quad-core, 2 hw threads/core → OS sees **8 logical processors**.
- **Two levels of scheduling** on such systems:
  1. OS decides which *software* thread runs on a *logical* CPU.
  2. Each core decides which of its *hardware* threads runs on the *physical* core.

### Load Balancing
- Needed on SMP to keep all CPUs evenly loaded.
- **Push migration** — a periodic task checks load per processor; pushes tasks from overloaded → other CPUs.
- **Pull migration** — an idle processor pulls a waiting task from a busy processor.

### Processor Affinity
- A thread that has run on a processor leaves its memory accesses in that processor's cache → **"processor affinity."**
- Load balancing can hurt affinity (migrating a thread loses its warmed cache).
- **Soft affinity** — OS *tries* to keep a thread on the same processor, no guarantee.
- **Hard affinity** — process can *explicitly specify* the set of processors it may run on.

### NUMA and CPU Scheduling
- A **NUMA-aware** OS assigns memory **closest** to the CPU the thread is running on (to minimize memory access latency).

## Real-Time CPU Scheduling
- **Soft real-time** — critical tasks get highest priority, but **no guarantee** on exact scheduling time.
- **Hard real-time** — task **must** be serviced by its deadline.
- **Event latency** = time from event occurrence → event serviced. Two components:
  1. **Interrupt latency** — time from interrupt arrival → start of the servicing routine.
  2. **Dispatch latency** — time for the scheduler to take the current process off CPU and switch to another.
- **Dispatch latency conflict phase** — two causes of delay:
  1. Preemption of any process running in **kernel mode**.
  2. Release by a **low-priority process** of resources needed by a **high-priority process**.

### Priority-Based Real-Time Scheduling
- Scheduler must support **preemptive, priority-based** scheduling — but this only guarantees **soft** real-time.
- Hard real-time additionally needs the ability to **meet deadlines**.
- Periodic process characteristics: processing time **t**, deadline **d**, period **p**; constraint **0 ≤ t ≤ d ≤ p**; rate = **1/p**.

### Rate Monotonic Scheduling (RMS)
- Priority ∝ **inverse of period**: **shorter period → higher priority**, longer period → lower priority.
- Can still **miss deadlines** (slide example: P2 misses its deadline at t=80).

### Earliest Deadline First (EDF)
- Priority assigned by **deadline**: earlier deadline → higher priority; later deadline → lower priority.
- (Dynamic priority, unlike RMS's static priority.)

> [!note]- Exam angle
> RMS (static priority by period) vs EDF (dynamic priority by deadline) — classic compare/contrast question, and know RMS can miss deadlines even when schedulable-looking.

### Proportional Share Scheduling
- **T** total shares allocated among all processes. An app gets **N** shares (N < T) → guaranteed **N/T** of total CPU time.

### POSIX Real-Time Scheduling (POSIX.1b)
- Two scheduling classes for real-time threads:
  - **SCHED_FIFO** — FCFS strategy, FIFO queue; **no time-slicing** among equal-priority threads.
  - **SCHED_RR** — like SCHED_FIFO but **time-slicing occurs** among equal-priority threads.
- Functions: `pthread_attr_getschedpolicy()`, `pthread_attr_setschedpolicy()`.

> [!note]- Exam angle
> SCHED_FIFO vs SCHED_RR — the only difference is time-slicing behavior among equal-priority threads. Easy to mix up with regular FIFO/RR from earlier in chapter — keep straight that these are POSIX *real-time* classes.

## OS Examples

### Linux Scheduling
- **Pre-2.5 kernel**: variation of standard UNIX scheduling algorithm.
- **2.5**: moved to **O(1) scheduling**.
  - Preemptive, priority-based; **two priority ranges**: real-time (**0–99**) and nice (**100–140**); numerically **lower = higher priority**.
  - Higher priority → larger quantum.
  - Task runnable while quantum remains (**active**); once **expired**, not runnable until all other tasks use their slices.
  - Per-CPU runqueue holds **two priority arrays** (active, expired), tasks indexed by priority; when active is empty, arrays are **swapped**.
  - Worked well overall but gave **poor response times for interactive processes** → replaced.
- **2.6.23+**: **Completely Fair Scheduler (CFS)**.
  - **Scheduling classes**, each with a priority; scheduler picks highest-priority task in the highest-priority class. Two classes: **default**, **real-time** (others addable).
  - Quantum based on **proportion of CPU time**, not fixed time allotments.
  - Nice value **−20 to +19** (lower = higher priority) → used to calculate **target latency** (interval during which a task should run at least once); target latency can grow as # active tasks grows.
  - CFS tracks per-task **vruntime** (virtual run time), with a **decay factor** by priority — **lower priority = higher decay rate**. Default priority → vruntime = actual run time.
  - Scheduler always picks the task with the **lowest vruntime** next.
- **Linux real-time scheduling**: follows POSIX.1b; real-time tasks have **static** priorities; real-time + normal map into **one global priority scheme**. Nice **−20 → global priority 100**; nice **+19 → global priority 139**.
- **Linux load balancing**: NUMA-aware. **Scheduling domain** = set of CPU cores balanced against one another, organized by what they share (e.g. cache memory) — goal is to **minimize thread migration between domains**.

> [!note]- Exam angle
> The O(1) → CFS transition (why it happened: poor interactive response times) + the vruntime "pick lowest" rule + nice-value→priority mapping numbers are all fair game individually.

### Windows Scheduling
- **Priority-based preemptive**; highest-priority thread runs next. **Dispatcher** = the scheduler.
- Thread runs until: (1) it **blocks**, (2) it **uses its time slice**, or (3) it's **preempted by a higher-priority thread**.
- Real-time threads can preempt non-real-time threads.
- **32-level priority scheme**: variable class = **1–15**, real-time class = **16–31**. **Priority 0** = memory-management thread. One queue per priority. If no runnable thread → runs the **idle thread**.
- **Priority classes** (Win32 API): `REALTIME_PRIORITY_CLASS`, `HIGH_PRIORITY_CLASS`, `ABOVE_NORMAL_PRIORITY_CLASS`, `NORMAL_PRIORITY_CLASS`, `BELOW_NORMAL_PRIORITY_CLASS`, `IDLE_PRIORITY_CLASS` — **all variable except REALTIME**.
- **Relative priority within a class**: `TIME_CRITICAL, HIGHEST, ABOVE_NORMAL, NORMAL, BELOW_NORMAL, LOWEST, IDLE`.
- Priority class + relative priority → combine into a numeric priority. **Base priority = NORMAL within the class.**
- If quantum expires → priority **lowered**, but never below base.
- If a wait occurs → priority **boosted** (amount depends on what was waited for). **Foreground window gets a 3× priority boost.**
- **Windows 7+**: added **User-Mode Scheduling (UMS)** — apps create/manage threads independent of the kernel; much more efficient for large thread counts; UMS schedulers come from language libraries (e.g. C++ **ConcRT**).

> [!note]- Exam angle
> Numbers to memorize: 32 levels, variable 1–15, real-time 16–31, priority 0 = memory mgmt thread, foreground boost = 3×.

### Solaris Scheduling
- Priority-based scheduling. **Six classes**: **Time Sharing (TS, default)**, **Interactive (IA)**, **Real Time (RT)**, **System (SYS)**, **Fair Share (FSS)**, **Fixed Priority (FP)**.
- A thread is in **one class at a time**; each class has its own scheduling algorithm.
- Time sharing = **multilevel feedback queue**, with a loadable table configurable by the sysadmin.
- Scheduler converts class-specific priorities into a **per-thread global priority** → highest-priority thread runs next; runs until it blocks / uses its time slice / is preempted by higher priority; **same-priority threads chosen via RR**.

## Algorithm Evaluation
- Approach: first determine criteria to use, then evaluate candidate algorithms against them.

### Deterministic Modeling
- Analytic evaluation using a specific, **predetermined workload**; defines each algorithm's performance for exactly that workload.
- Simple & fast, but requires exact input numbers and results apply **only** to that specific input.
- Example (5 processes arriving at t=0): **FCFS = 28ms**, **non-preemptive SJF = 13ms**, **RR = 23ms** (min avg waiting times).

### Queueing Models
- Describe process arrivals + CPU/I/O bursts **probabilistically** (commonly exponential, described by a mean).
- System modeled as a **network of servers**, each with its own queue.
- Given arrival & service rates → compute utilization, avg queue length, avg wait time, etc.

### Little's Formula
- **n** = average queue length, **W** = average waiting time in queue, **λ** = average arrival rate into queue.
- **Little's Law: n = λ × W** — holds in steady state (processes leaving = processes arriving); valid for **any** scheduling algorithm and **any** arrival distribution.
- Example: λ = 7 processes/sec, n = 14 processes in queue → **W = 2 seconds**.

> [!note]- Exam angle
> Little's Law numeric plug-in (n = λW) is a guaranteed easy mark — memorize the formula and variable meanings exactly.

### Simulations
- More accurate than queueing models (which are limited).
- Programmed model of the computer system; clock is a variable; gathers performance statistics.
- Data to drive the simulation comes from:
  - Random number generator per known probability distributions,
  - Distributions defined mathematically or empirically,
  - **Trace tapes** — record sequences of real events from real systems.

### Implementation
- Even simulations have limited accuracy — ultimately, **implement the scheduler and test in real systems**.
- High cost, high risk; environments vary.
- Most flexible schedulers can be modified **per-site/per-system**, or via **APIs to adjust priorities** — but environments still vary.
