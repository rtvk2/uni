# Chapter 5: CPU Scheduling — Complete Notes

Voice-reader version. The technical content and order are preserved as closely as possible. Where the original notes contain a diagram, graph, Gantt chart, or code block, this version tells you to refer to it on the relevant slide.

## Part 1: Basic Concepts (Slides 1–9)

### Slide 1: Title
Chapter 5: CPU Scheduling — Silberschatz, Galvin, Gagne.

### Slide 2: Outline
1. Basic Concepts
2. Scheduling Criteria
3. Scheduling Algorithms
4. Thread Scheduling
5. Multi-Processor Scheduling
6. Real-Time CPU Scheduling
7. Operating Systems Examples
8. Algorithm Evaluation

### Slide 3: Objectives
- Describe various CPU scheduling algorithms.
- Assess CPU scheduling algorithms based on scheduling criteria.
- Explain issues related to multiprocessor and multicore scheduling.
- Describe various real-time scheduling algorithms.
- Describe scheduling algorithms used in Windows, Linux, and Solaris.
- Apply modeling and simulations to evaluate CPU scheduling algorithms.

### Slide 4: Basic Concepts — CPU-I/O Burst Cycle
- Maximum CPU utilization obtained with multiprogramming.
- Process execution consists of a cycle of CPU execution and I/O wait.
- CPU burst followed by I/O burst.
- CPU burst distribution is of main concern.

Diagram:
Refer to the diagram on Slide 4.

### Slide 5: Histogram of CPU-Burst Times
- Large number of short CPU bursts.
- Small number of long CPU bursts.
- Graph: frequency vs burst duration, peaked near short durations.

### Slide 6: CPU Scheduler
- The CPU scheduler selects from among the processes in ready queue and allocates a CPU core to one of them.
- Queue may be ordered in various ways.
- CPU scheduling decisions may take place when a process:
  1. Switches from running to waiting state.
  2. Switches from running to ready state.
  3. Switches from waiting to ready.
  4. Terminates.
- For situations 1 and 4, there is no choice — a new process must be selected.
- For situations 2 and 3, there is a choice.

### Slide 7: Preemptive vs Nonpreemptive
- Nonpreemptive scheduling: scheduling takes place only under circumstances 1 and 4.
  - Once CPU allocated, process keeps CPU until it releases it (terminates or switches to waiting).
- Preemptive scheduling: otherwise.
- Virtually all modern OSes (Windows, macOS, Linux, UNIX) use preemptive scheduling.

### Slide 8: Race Conditions
- Preemptive scheduling can result in race conditions when data are shared among several processes.
- Example: one process updating data is preempted; second process reads inconsistent data.
- Detailed in Chapter 6.

### Slide 9: Dispatcher
- Dispatcher module gives control of CPU to the process selected by the CPU scheduler. It involves:
  - Switching context.
  - Switching to user mode.
  - Jumping to proper location in user program.
- Dispatcher latency: time it takes for dispatcher to stop one process and start another running.

Diagram:
Refer to the diagram on Slide 9.

Dispatch latency = time between save and restore.

## Part 2: Scheduling Criteria (Slides 10–11)

### Slide 10: Scheduling Criteria
- CPU utilization – keep CPU as busy as possible.
- Throughput – number of processes that complete their execution per time unit.
- Turnaround time – amount of time to execute a particular process.
- Waiting time – amount of time a process has been waiting in the ready queue.
- Response time – amount of time from when a request was submitted until the first response is produced.

### Slide 11: Optimization Goals
- Max CPU utilization.
- Max throughput.
- Min turnaround time.
- Min waiting time.
- Min response time.

## Part 3: Scheduling Algorithms (Slides 12–32)

### Slide 12: First-Come, First-Served (FCFS) — Example 1
Processes: P1 (24), P2 (3), P3 (3). Order: P1, P2, P3.

Gantt chart:
Refer to the diagram on Slide 12.

Waiting times: P1 = 0; P2 = 24; P3 = 27.
Average waiting time = (0 + 24 + 27) / 3 = 17.

### Slide 13: FCFS — Example 2
Order: P2, P3, P1.

Gantt chart:
Refer to the diagram on Slide 13.

Waiting times: P1 = 6; P2 = 0; P3 = 3.
Average waiting time = (6 + 0 + 3) / 3 = 3.

Convoy effect: short process behind long process. Consider one CPU-bound and many I/O-bound processes.

### Slide 14: Shortest-Job-First (SJF)
- Associate with each process the length of its next CPU burst.
- Use these lengths to schedule the process with the shortest time.
- SJF is optimal — gives minimum average waiting time for a given set of processes.
- Preemptive version called shortest-remaining-time-first (SRTF).
- How to determine length? Ask user or estimate.

### Slide 15: SJF Example
Processes: P1 (6), P2 (8), P3 (7), P4 (3).

Gantt chart:
Refer to the diagram on Slide 15.

Waiting times: P4 = 0; P1 = 3; P3 = 9; P2 = 16.
Average waiting time = (3 + 16 + 9 + 0) / 4 = 7.

### Slide 16: Exponential Averaging for SJF
- Can only estimate the length — should be similar to previous one.
- Then pick process with shortest predicted next CPU burst.
- Formula:
  
  tau n+1 = alpha t n + (1 - alpha)tau n
  
  where:
  - t n = actual length of n to the power of th CPU burst.
  - tau n+1 = predicted value for next CPU burst.
  - tau n = previous prediction.
  - 0 \le alpha \le 1.
- Commonly, alpha = 1 divided by 2.

### Slide 17: Exponential Averaging Graph
- Graph shows actual CPU burst (t) and guess (τ) over time.
- Example values: t = 6, 4, 6, 4, 13, 13, 13; guess = 10, 8, 6, 6, 5, 9, 11, 12.

### Slide 18: Exponential Averaging — Alpha Values
- alpha = 0: tau n+1 = tau n — recent history does not count.
- alpha = 1: tau n+1 = alpha t n — only actual last CPU burst counts.
- Expanding:
  
  tau n+1 = alpha t n + (1-alpha)alpha t n-1 + and so on + (1-alpha)^j alpha t n-j + and so on + (1-alpha)^n+1tau 0
  
- Since both alpha and 1-alpha are ≤ 1, each successor term has less weight than its predecessor.

### Slide 19: Shortest-Remaining-Time-First (SRTF)
- Preemptive version of SJF.
- Whenever a new process arrives in ready queue, decision is redone using SJF.
- Is SRT more “optimal” than SJF in terms of minimum average waiting time? Yes, preemptive can be more optimal.

### Slide 20: SRTF Example
Processes:
- Process: P1; Arrival: 0; Burst: 8.
- Process: P2; Arrival: 1; Burst: 4.
- Process: P3; Arrival: 2; Burst: 9.
- Process: P4; Arrival: 3; Burst: 5.

Preemptive SJF Gantt chart:
Refer to the diagram on Slide 20.

Waiting times:
- P1 = (10-1) = 9
- P2 = (1-1) = 0
- P3 = (17-2) = 15
- P4 = (5-3) = 2
Average waiting = (9 + 0 + 15 + 2) / 4 = 26/4 = 6.5.

### Slide 21: Round-Robin (RR)
- Each process gets a small unit of CPU time (time quantum q), usually 10–100 ms.
- After time elapsed, process is preempted and added to end of ready queue.
- If n processes in ready queue and quantum q, each process gets 1/n of CPU time in chunks of at most q.
- No process waits more than (n-1)q time units.
- Timer interrupts every quantum to schedule next process.
- Performance:
  - q large → FIFO (FCFS).
  - q small → RR.
- q must be large with respect to context switch, otherwise overhead too high.

### Slide 22: RR Example (q = 4)
Processes: P1 (24), P2 (3), P3 (3).

Gantt chart:
Refer to the diagram on Slide 22.

Waiting times:
- P1 = 6 (waits from 4 to 10)
- P2 = 4 (waits from 0 to 4)
- P3 = 7 (waits from 0 to 7)
Average = (6+4+7)/3 ≈ 5.67.
Typically higher average turnaround than SJF, but better response.

### Slide 23: Time Quantum and Context Switch Time
- Graph shows process time = 10, quantum = 12, 6, 1.
- Quantum 12: 0 context switches.
- Quantum 6: 1 context switch.
- Quantum 1: 9 context switches.
- Context switch overhead increases as quantum decreases.

### Slide 24: 80% Rule
- 80% of CPU bursts should be shorter than q.

### Slide 25: Priority Scheduling
- A priority number (integer) associated with each process.
- CPU allocated to process with highest priority (smallest integer = highest priority).
- Preemptive or nonpreemptive.
- SJF is priority scheduling where priority is inverse of predicted next CPU burst time.
- Problem = Starvation — low priority processes may never execute.
- Solution = Aging — as time progresses increase priority of process.

### Slide 26: Priority Scheduling Example
Processes:
- Process: P1; Burst: 10; Priority: 3.
- Process: P2; Burst: 1; Priority: 1.
- Process: P3; Burst: 2; Priority: 4.
- Process: P4; Burst: 1; Priority: 5.
- Process: P5; Burst: 5; Priority: 2.

Gantt chart:
Refer to the diagram on Slide 26.

Waiting times: P2=0, P5=1, P1=6, P3=16, P4=18.
Average = (0+1+6+16+18)/5 = 8.2.

### Slide 27: Priority Scheduling with Round-Robin
- Run process with highest priority.
- Processes with same priority run round-robin.
- Example: P1 (4, prio 3), P2 (5, prio 2), P3 (8, prio 2), P4 (7, prio 1), P5 (3, prio 3). Quantum = 2.
- Gantt chart: P4 (0-7), P2 (7-9), P3 (9-11), P2 (11-13), P3 (13-15), P2 (15-16), P3 (16-18), P1 (18-20), P5 (20-22), P1 (22-24), P5 (24-25).

### Slide 28: Multilevel Queue
- Ready queue consists of multiple queues.
- Scheduler defined by:
  - Number of queues.
  - Scheduling algorithms for each queue.
  - Method to determine which queue a process enters.
  - Scheduling among queues.

### Slide 29: Multilevel Queue — Priority Queues
- Separate queues for each priority.
- Schedule process in highest-priority queue.
- Diagram: priority 0, 1, 2, …, n queues.

### Slide 30: Multilevel Queue — Process Type
- Prioritization based upon process type:
  - highest priority: real-time processes
  - system processes
  - interactive processes
  - batch processes (lowest)

### Slide 31: Multilevel Feedback Queue (MLFQ)
- A process can move between various queues.
- Scheduler defined by:
  - Number of queues.
  - Scheduling algorithms for each queue.
  - Method to determine when to upgrade a process.
  - Method to determine when to demote a process.
  - Method to determine which queue a process enters.
- Aging can be implemented using MLFQ.

### Slide 32: MLFQ Example
- Three queues:
  - Q0: RR with quantum 8 ms.
  - Q1: RR with quantum 16 ms.
  - Q2: FCFS.
- Scheduling:
  - New process enters Q0, served RR.
  - If not finished in 8 ms, moved to Q1.
  - At Q1, served RR, receives 16 additional ms.
  - If still not complete, preempted and moved to Q2.

## Part 4: Thread Scheduling (Slides 33–36)

### Slide 33: Thread Scheduling
- Distinction between user-level and kernel-level threads.
- When threads supported, threads scheduled, not processes.
- Many-to-one and many-to-many models: thread library schedules user-level threads to run on LWP.
  - Known as process-contention scope (PCS) — competition within process.
  - Typically done via priority set by programmer.
- Kernel thread scheduled onto available CPU is system-contention scope (SCS) — competition among all threads in system.

### Slide 34: Pthread Scheduling API
- API allows specifying either PCS or SCS during thread creation:
  - PTHREAD SCOPE_PROCESS schedules threads using PCS.
  - PTHREAD SCOPE_SYSTEM schedules threads using SCS.
- Can be limited by OS — Linux and macOS only allow PTHREAD SCOPE_SYSTEM.

### Slide 35: Pthread Scope Code — Get Scope
Refer to the code on Slide 35.

### Slide 36: Pthread Scope Code — Set Scope
Refer to the code on Slide 36.

## Part 5: Multi-Processor Scheduling (Slides 37–45)

### Slide 37: Multi-Processor Scheduling — Intro
- CPU scheduling more complex when multiple CPUs available.
- Multiprocess may be:
  - Multicore CPUs
  - Multithreaded cores
  - NUMA systems
  - Heterogeneous multiprocessing

### Slide 38: Symmetric Multiprocessing (SMP)
- Each processor is self-scheduling.
- All threads may be in a common ready queue (a).
- Each processor may have its own private queue of threads (b).

Diagram:
- (a) common ready queue → cores 0..n
- (b) per-core run queues → core0, core1, ..., coren

### Slide 39: Multicore Programming
- Recent trend: multiple processor cores on same physical chip.
- Faster and consumes less power.
- Multiple threads per core also growing.
- Takes advantage of memory stall to make progress on another thread while memory retrieve happens.

Diagram: Thread alternates compute cycle (C) and memory stall cycle (M).

### Slide 40: Hardware Threads
- Each core has > 1 hardware threads.
- If one thread has a memory stall, switch to another thread.

Diagram: thread1 and thread0 interleaved C and M cycles.

### Slide 41: Chip-Multithreading (CMT)
- CMT assigns each core multiple hardware threads (Intel calls this hyperthreading).
- On a quad-core system with 2 hardware threads per core, OS sees 8 logical processors.

Diagram: processor with core0..3 each with 2 hardware threads; OS view CPU0..CPU7.

### Slide 42: Two Levels of Scheduling
1. OS decides which software thread to run on a logical CPU.
2. Each core decides which hardware thread to run on the physical core.

Diagram: software threads → hardware threads (logical processors) → processing core.

### Slide 43: Load Balancing
- If SMP, need to keep all CPUs loaded for efficiency.
- Load balancing attempts to keep workload evenly distributed.
- Push migration — periodic task checks load on each processor, pushes task from overloaded CPU to other CPUs.
- Pull migration — idle processor pulls waiting task from busy processor.

### Slide 44: Processor Affinity
- When a thread has been running on one processor, the cache contents of that processor stores memory accesses by that thread.
- Thread has affinity for a processor.
- Load balancing may affect processor affinity.
- Soft affinity — OS attempts to keep thread on same processor, but no guarantees.
- Hard affinity — allows a process to specify a set of processors it may run on.

### Slide 45: NUMA and Scheduling
- If OS is NUMA-aware, it will assign memory closest to the CPU the thread is running on.
- Diagram: CPU → fast access → memory; CPU to other memory = slow access.

## Part 6: Real-Time CPU Scheduling (Slides 46–57)

### Slide 46: Real-Time CPU Scheduling — Intro
- Can present obvious challenges.
- Soft real-time systems — critical real-time tasks have highest priority, but no guarantee as to when tasks will be scheduled.
- Hard real-time systems — task must be serviced by its deadline.

### Slide 47: Latency
1. Interrupt latency — time from arrival of interrupt to start of routine that services interrupt.
2. Dispatch latency — time for schedule to take current process off CPU and switch to another.

Diagram: event E first occurs at t0; real-time system responds at t1; event latency = t1 - t0.

### Slide 48: Interrupt Latency
- Diagram showing interrupt latency.

### Slide 49: Dispatch Latency
- Two components:
  1. Preemption of any process running in kernel mode.
  2. Release by low-priority process of resources needed by high-priority processes.
- Diagram: response interval = interrupt processing + dispatch latency + real-time process execution.

### Slide 50: Real-Time Scheduling
- For real-time scheduling, scheduler must support preemptive, priority-based scheduling.
  - But only guarantees soft real-time.
- For hard real-time must also provide ability to meet deadlines.
- Processes have new characteristics: periodic ones require CPU at constant intervals.
  - Has processing time t, deadline d, period p.
  - 0 \le t \le d \le p.
  - Rate of periodic task is 1/p.

Diagram: p, d, t intervals across periods.

### Slide 51: Periodic Task Diagram
- Diagram showing periods, deadlines, processing times.

### Slide 52: Missed Deadline Example
- Process P2 misses finishing its deadline at time 80.
- Diagram shows deadlines.

### Slide 53: Earliest Deadline First (EDF)
- Priorities assigned according to deadlines:
  - Earlier deadline → higher priority.
  - Later deadline → lower priority.
- Diagram shows all deadlines met.

### Slide 54: Proportional Share Scheduling
- T shares allocated among all processes in system.
- An application receives N shares where N < T.
- Ensures each application receives N/T of total processor time.

### Slide 55: POSIX Real-Time Scheduling
- POSIX.1b standard.
- API provides functions for managing real-time threads.
- Defines two scheduling classes:
  1. SCHED FIFO — FCFS with FIFO queue; no time-slicing for equal priority.
  2. SCHED RR — similar to FIFO except time-slicing for equal priority.
- Functions:
  - pthread attr_getsched policy(pthread attr_t *attr, int *policy)
  - pthread attr_setsched policy(pthread attr_t *attr, int policy)

### Slide 56: POSIX RT Code — Get Policy
Refer to the code on Slide 56.

### Slide 57: POSIX RT Code — Set Policy
Refer to the code on Slide 57.

## Part 7: OS Examples (Slides 58–72)

### Slide 58: OS Examples — Outline
- Linux scheduling
- Windows scheduling
- Solaris scheduling

### Slide 59: Linux Scheduling — O(1) Scheduler
- Prior to kernel version 2.5, ran variation of standard UNIX scheduling algorithm.
- Version 2.5 moved to constant order O(1) scheduling time.
- Preemptive, priority based.
- Two priority ranges: time-sharing and real-time.
- Real-time range from 0 to 99; nice value from 100 to 140.
- Map into global priority with numerically lower values indicating higher priority.
- Higher priority gets larger q.
- Task run-able as long as time left in time slice (active).
- If no time left (expired), not run-able until all other tasks use their slices.
- All run-able tasks tracked in per-CPU runqueue data structure.
- Two priority arrays (active, expired).
- Tasks indexed by priority.
- When no more active, arrays are exchanged.
- Worked well, but poor response times for interactive processes.

### Slide 60: Linux CFS (Completely Fair Scheduler)
- Scheduling classes — each has specific priority.
- Scheduler picks highest priority task in highest scheduling class.
- Rather than quantum based on fixed time allotments, based on proportion of CPU time.
- Two scheduling classes included, others can be added:
  1. default
  2. real-time

### Slide 61: Linux CFS — Virtual Runtime
- Quantum calculated based on nice value from -20 to +19.
- Lower value is higher priority.
- Calculates target latency — interval of time during which task should run at least once.
- Target latency can increase if number of active tasks increases.
- CFS scheduler maintains per task virtual run time in variable vruntime.
- Associated with decay factor based on priority of task — lower priority is higher decay rate.
- Normal default priority yields virtual run time = actual run time.
- To decide next task to run, scheduler picks task with lowest virtual run time.

### Slide 62: Linux CFS — Red-Black Tree
- Each runnable task is placed in a red-black tree — balanced binary search tree whose key is based on vruntime.
- Task with smallest vruntime is leftmost node.
- When task becomes runnable, added to tree.
- If task not runnable, removed.
- Tasks with smaller vruntime are toward left; larger toward right.
- Leftmost node has smallest key = highest priority.
- Navigating to leftmost node requires O(log base 2 N) operations.
- Linux scheduler caches this value in rb leftmost; next task requires only retrieve cached value.

### Slide 63: Linux Scheduling — Priorities
- Real-time scheduling according to POSIX.1b.
- Real-time tasks have static priorities.
- Real-time plus normal map into global priority scheme.
- Nice value of -20 maps to global priority 100.
- Nice value of +19 maps to priority 139.
- Table: Real-Time 0–99 (higher priority), Normal 100–139 (lower priority).

### Slide 64: Linux Scheduling — NUMA and Domains
- Linux supports load balancing, but is also NUMA-aware.
- Scheduling domain is a set of CPU cores that can be balanced against one another.
- Domains organized by what they share (cache memory).
- Goal: keep threads from migrating between domains.
- Diagram: physical processor domain (NUMA node) with cores and L2, L3.

### Slide 65: Windows Scheduling
- Windows uses priority-based preemptive scheduling.
- Highest-priority thread runs next.
- Dispatcher is scheduler.
- Thread runs until: (1) blocks, (2) uses time slice, (3) preempted by higher-priority thread.
- Real-time threads can preempt non-real-time.
- 32-level priority scheme.
- Variable class is 1–15, real-time class is 16–31.
- Priority 0 is memory-management thread.
- Queue for each priority.
- If no run-able thread, runs idle thread.

### Slide 66: Windows Priority Classes
- Win32 API identifies priority classes:
  - REALTIME PRIORITY_CLASS, HIGH PRIORITY_CLASS, ABOVE NORMAL_PRIORITY CLASS, NORMAL PRIORITY_CLASS, BELOW NORMAL_PRIORITY CLASS, IDLE PRIORITY_CLASS.
- All are variable except REALTIME.
- A thread within a given priority class has a relative priority:
  - TIME CRITICAL, HIGHEST, ABOVE NORMAL, NORMAL, BELOW NORMAL, LOWEST, IDLE.
- Priority class and relative priority combine to give numeric priority.
- Base priority is NORMAL within the class.
- If quantum expires, priority lowered, but never below base.

### Slide 67: Windows Priority Boosts
- If wait occurs, priority boosted depending on what was waited for.
- Foreground window given 3× priority boost.
- Windows 7 added user-mode scheduling (UMS).
- Applications create and manage threads independent of kernel.
- For large number of threads, much more efficient.
- UMS schedulers come from programming language libraries like C++ Concurrent Runtime (ConcRT) framework.

### Slide 68: Windows Priorities Table
Table of numeric priorities for real-time, high, above normal, normal, below normal, idle classes and relative priorities.

### Slide 69: Solaris Scheduling
- Priority-based scheduling.
- Six classes available:
  - Time sharing (default) (TS)
  - Interactive (IA)
  - Real time (RT)
  - System (SYS)
  - Fair Share (FSS)
  - Fixed priority (FP)
- Given thread can be in one class at a time.
- Each class has its own scheduling algorithm.
- Time sharing is multi-level feedback queue.
- Loadable table configurable by sysadmin.

### Slide 70: Solaris Dispatch Table
Table with priority, time quantum, time quantum expired, return from sleep.

### Slide 71: Solaris Scheduling Diagram
- Global priority from 0 to 169.
- Interrupt threads (169–160), realtime (159–100), system (99–60), fair share/fixed/timeshare/interactive (59–0).
- Scheduling order: first to last.

### Slide 72: Solaris Scheduling — Priority Conversion
- Scheduler converts class-specific priorities into per-thread global priority.
- Thread with highest priority runs next.
- Runs until: (1) blocks, (2) uses time slice, (3) preempted by higher-priority thread.
- Multiple threads at same priority selected via RR.

## Part 8: Algorithm Evaluation (Slides 73–79)

### Slide 73: Algorithm Evaluation
- How to select CPU-scheduling algorithm for an OS?
- Determine criteria, then evaluate algorithms.
- Deterministic modeling — analytic evaluation.
  - Takes a particular predetermined workload and defines performance of each algorithm for that workload.
- Consider 5 processes arriving at time 0:
- Process: P1; Burst: 10.
- Process: P2; Burst: 29.
- Process: P3; Burst: 3.
- Process: P4; Burst: 7.
- Process: P5; Burst: 12.

### Slide 74: Deterministic Modeling Example
- For each algorithm, calculate minimum average waiting time.
- FCFS is 28 ms:
Refer to the diagram on Slide 74.

- Non-preemptive SJF is 13 ms:
Refer to the diagram on Slide 74.

- RR is 23 ms:
Refer to the diagram on Slide 74.

- Simple and fast, but requires exact numbers for input, applies only to those inputs.

### Slide 75: Queueing Models
- Describes arrival of processes, and CPU and I/O bursts probabilistically.
- Commonly exponential, described by mean.
- Computes average throughput, utilization, waiting time, etc.
- Computer system described as network of servers, each with queue of waiting processes.
- Knowing arrival rates and service rates, computes utilization, average queue length, average wait time, etc.

### Slide 76: Little’s Formula
- n = average queue length.
- W = average waiting time in queue.
- lambda = average arrival rate into queue.
- Little’s law — in steady state, processes leaving queue must equal processes arriving:
  
  n = lambda times W
  
- Valid for any scheduling algorithm and arrival distribution.
- Example: if on average 7 processes arrive per second, and normally 14 processes in queue, then average wait time per process = 14 divided by 7 = 2 seconds.

### Slide 77: Simulation
- Even simulations have limited accuracy.
- Just implement new scheduler and test in real systems.
- High cost, high risk.
- Environments vary.
- Most flexible schedulers can be modified per-site or per-system.
- Or APIs to modify priorities.
- But again environments vary.

### Slide 78: Evaluation of CPU Schedulers by Simulation
- Diagram/graph: simulation evaluation.

### Slide 79: Implementation
- Even simulations have limited accuracy.
- Just implement new scheduler and test in real systems.
- High cost, high risk.
- Environments vary.
- Most flexible schedulers can be modified per-site or per-system.
- Or APIs to modify priorities.
- But again environments vary.

### Slide 80: End of Chapter 5

## End of Chapter 5 Notes

This completes all slides in Chapter 5: CPU Scheduling.
