---
title: "OS Ch6 — Synchronization Tools"
course: Operating Systems
chapter: 6
tags: [os, exam-prep, synchronization, concurrency]
---

## Background
- Concurrent processes may be interrupted at any time → partial completion.
- Concurrent access to shared data → potential **data inconsistency**.
- Need mechanisms to ensure **orderly execution** of cooperating processes.
- Ch4's bounded-buffer counter (updated concurrently by producer & consumer) → **race condition** example.

## Race Condition — fork() example
- P0, P1 both create children via `fork()`; race on kernel variable `next_available_pid`.
- Without a mutual-exclusion mechanism, **the same pid could be assigned to two different processes**.

## Critical Section Problem
- System of n processes {p0…pn−1}; each has a **critical section** (CS) — code changing shared vars/tables/files.
- **When one process is in its CS, no other process may be in its CS.**
- General structure per process: `while(true){ entry section; critical section; exit section; remainder section; }`

### Three requirements for a CS-problem solution
1. **Mutual Exclusion** — if Pi is executing in CS, no other process can be in its CS.
2. **Progress** — if no process is in CS and some processes wish to enter, selection of who enters next **cannot be postponed indefinitely**.
3. **Bounded Waiting** — a **bound** must exist on how many times other processes may enter their CS after a process requests entry and before that request is granted. (Assumes each process executes at nonzero speed; **no assumption** about relative speeds.)

> [!note]- Exam angle
> All three requirements, verbatim, are a guaranteed definition question. Also expect "does solution X satisfy Progress/Bounded Waiting?" as a follow-up on every algorithm below.

## Interrupt-Based Solution
- Entry section: disable interrupts. Exit section: enable interrupts.
- **Does NOT fully solve the problem**:
  - CS running for a long time (e.g. an hour) blocks everything.
  - Processes can **starve**.
  - Doesn't work correctly with **multiple CPUs** (disabling interrupts on one CPU doesn't stop others).

## Software Solution 1 (`turn` variable only)
- Two-process solution; assumes `load`/`store` are atomic.
- Shared: `int turn;` initially = i. Algorithm per Pi: `while(turn==j); CS; turn=j; remainder;`
- **Mutual exclusion holds** (turn can't be both 0 and 1).
- **Fails Progress** — strict alternation forces a process to wait even when the other doesn't want to enter its CS.

## Peterson's Solution
- Two-process; assumes atomic `load`/`store`.
- Shared: `int turn; boolean flag[2];` — `flag[i]=true` ⇒ Pi ready to enter CS.
- Algorithm per Pi: `flag[i]=true; turn=j; while(flag[j] && turn==j); CS; flag[i]=false; remainder;`
- **Provably satisfies all 3 requirements**:
  - Mutual exclusion: Pi enters CS only if `flag[j]==false` **or** `turn==i`.
  - Progress ✓, Bounded waiting ✓.

> [!note]- Exam angle
> Be able to **write Peterson's algorithm from memory** and explain *why* each of the 3 CS requirements holds — very commonly asked in full.

### Peterson's Solution and Modern Architecture
- **Not guaranteed to work on modern architectures** — processors/compilers may **reorder** operations that have no data dependency (to improve performance).
- Single-threaded: fine (result always the same). **Multithreaded: reordering can cause inconsistent/unexpected results.**
- Example: Thread1 does `while(!flag); print x;`; Thread2 does `x=100; flag=true;`. Expected output = 100, but if Thread2's two independent stores (`flag=true`, `x=100`) are reordered, Thread1 can see `flag=true` before `x=100` is visible → **prints 0**.
- Diagram: with reordering, `turn=1` / `flag[0]=true` and `turn=0,flag[1]=true` can interleave such that **both processes end up in CS simultaneously** — breaks mutual exclusion.

### Memory Barrier
- **Memory model** = guarantees a computer architecture makes to application programs about memory visibility.
  - **Strongly ordered** — a modification by one processor is **immediately visible** to all others.
  - **Weakly ordered** — a modification **may not be immediately visible** to others.
- **Memory barrier** = instruction forcing any memory change to be **propagated (made visible)** to all other processors; ensures all loads/stores complete **before** any subsequent load/store.
- Fix for the example above: Thread1 does `while(!flag) memory_barrier(); print x;`; Thread2 does `x=100; memory_barrier(); flag=true;` — guarantees ordering is preserved, output = 100.

> [!note]- Exam angle
> Strongly vs weakly ordered memory models, and *why* Peterson's/similar algorithms need memory barriers on real hardware, is a common "modern relevance" question.

## Synchronization Hardware
- Uniprocessors could disable interrupts — but generally too inefficient / not scalable on multiprocessor systems.
- **Three forms of hardware support** covered: **hardware instructions**, **atomic variables** (memory barriers already covered above).

### `test_and_set()` instruction
```
boolean test_and_set(boolean *target) {
    boolean rv = *target;
    *target = true;
    return rv;
}
```
- Properties: executed **atomically**; returns the **original** value; sets target to **true**.
- Solution: shared `boolean lock = false;` → `while(test_and_set(&lock)); CS; lock=false;`

### `compare_and_swap()` (CAS) instruction
```
int compare_and_swap(int *value, int expected, int new_value) {
    int temp = *value;
    if (*value == expected) *value = new_value;
    return temp;
}
```
- Properties: atomic; returns **original** value of `*value`; swap happens **only if** `*value == expected`.
- Solution: shared `int lock = 0;` → `while(compare_and_swap(&lock,0,1) != 0); CS; lock=0;`

### Bounded-waiting CAS solution
- Adds `waiting[]` array + `key` so no process waits more than n−1 turns — satisfies bounded waiting (unlike the plain CAS lock above, which does **not** guarantee bounded waiting by itself).
```
while (true) {
   waiting[i] = true; key = 1;
   while (waiting[i] && key == 1)
       key = compare_and_swap(&lock,0,1);
   waiting[i] = false;
   /* CS */
   j = (i+1) % n;
   while ((j != i) && !waiting[j]) j = (j+1) % n;
   if (j == i) lock = 0; else waiting[j] = false;
   /* remainder */
}
```

> [!note]- Exam angle
> Plain test-and-set / CAS locks satisfy Mutual Exclusion + Progress but **NOT Bounded Waiting** by themselves — the extended waiting[] version is what fixes that. This distinction is a classic trick question.

### Atomic Variables
- Built on top of instructions like CAS; provide atomic (uninterruptible) updates to basic types (int, bool).
```
void increment(atomic_int *v) {
    int temp;
    do { temp = *v; }
    while (temp != compare_and_swap(v, temp, temp+1));
}
```

## Mutex Locks
- Simplest OS-designer-provided tool (previous hardware-only solutions are inaccessible to app programmers).
- Boolean variable indicating lock availability. Protect CS via **`acquire()`** then **`release()`**.
- `acquire()`/`release()` themselves must be atomic (usually implemented via CAS).
- Because acquiring busy-waits, this kind of lock is called a **spinlock**.

## Semaphores
- More sophisticated sync tool than mutex locks. **Semaphore S** = integer variable.
- Accessed only via two **atomic** ops: **`wait()`** (originally **P()**) and **`signal()`** (originally **V()**).
```
wait(S)   { while (S <= 0); S--; }
signal(S) { S++; }
```
- **Counting semaphore** — value ranges over unrestricted domain.
- **Binary semaphore** — value only 0 or 1 → **same as a mutex lock**. (A counting semaphore can be implemented using a binary semaphore.)

### Semaphore Usage
- **Mutual exclusion**: semaphore `mutex` init to 1 → `wait(mutex); CS; signal(mutex);`
- **Ordering** (ensure S1 in P1 happens before S2 in P2): semaphore `synch` init to 0.
  - P1: `S1; signal(synch);`
  - P2: `wait(synch); S2;`

> [!note]- Exam angle
> Two canonical semaphore patterns — mutual exclusion (init=1) vs. execution ordering (init=0) — are the basis of almost every synchronization problem asked (producer-consumer, readers-writers, etc.). Know both cold.

### Semaphore Implementation
- Must guarantee no two processes execute `wait()`/`signal()` on the **same semaphore simultaneously** → implementing wait/signal is itself a critical-section problem!
- Naive implementation can busy-wait inside wait/signal (ok if CS rarely occupied, bad if apps spend lots of time in CS — **not a good general solution**).

### No-Busy-Wait Implementation
```
typedef struct { int value; struct process *list; } semaphore;

wait(semaphore *S) {
    S->value--;
    if (S->value < 0) { add this process to S->list; block(); }
}
signal(semaphore *S) {
    S->value++;
    if (S->value <= 0) { remove a process P from S->list; wakeup(P); }
}
```
- **block()** — suspends the calling process, puts it on the semaphore's waiting queue.
- **wakeup(P)** — moves process P from waiting queue to ready queue.
- (Implication: a negative `S->value` = the number of processes currently waiting on that semaphore.)

### Problems with Semaphores (misuse patterns)
- `signal(mutex) … wait(mutex)` — **wrong order**.
- `wait(mutex) … wait(mutex)` — double-lock (self-deadlock).
- **Omitting** `wait(mutex)` and/or `signal(mutex)` entirely.
- These (and others) are examples of what goes wrong when sync tools are used incorrectly by programmers — motivates higher-level tools like monitors.

## Monitors
- High-level abstraction for process synchronization.
- **Abstract data type** — internal (shared) variables accessible **only** by code inside the monitor's own procedures.
- **Only one process may be active inside the monitor at a time** (mutual exclusion is automatic/built-in).
```
monitor monitor-name {
    // shared variable declarations
    procedure P1(...) {...}
    procedure P2(...) {...}
    ...
    initialization code (...) {...}
}
```
- **Monitor implementation using semaphores**: `semaphore mutex = 1;` wrap every procedure body: `wait(mutex); body of P; signal(mutex);` — this is how mutual exclusion inside the monitor is actually enforced under the hood.

### Condition Variables
- Declared as `condition x, y;` — needed because a monitor alone can't let a process wait for a specific internal condition.
- Two ops:
  - **`x.wait()`** — invoking process is **suspended** until `x.signal()` is called.
  - **`x.signal()`** — resumes **one** waiting process (if any). **If nobody is waiting on x, `signal()` has no effect** (this differs from semaphore `signal()`, which always increments!).

> [!note]- Exam angle
> Key contrast: semaphore `signal()` always affects the semaphore's value even if nobody is waiting; condition-variable `signal()` does **nothing** if no process is waiting. Common trip-up.

### Usage Example (S1 before S2, via monitor)
- Monitor with procedures F1 (called by P1), F2 (called by P2); condition `x` (init 0), boolean `done`.
```
F1: S1; done = true; x.signal();
F2: if (done == false) x.wait(); S2;
```

### Monitor Implementation Using Semaphores (with condition variables)
- Variables: `semaphore mutex=1; semaphore next=0; int next_count=0;` (next_count = # processes waiting inside monitor, ready to resume the signaler).
- Every monitor procedure wrapped: `wait(mutex); body of P; if(next_count>0) signal(next); else signal(mutex);`
- For each condition variable x: `semaphore x_sem=0; int x_count=0;`
```
x.wait():
    x_count++;
    if (next_count > 0) signal(next); else signal(mutex);
    wait(x_sem);
    x_count--;

x.signal():
    if (x_count > 0) {
        next_count++;
        signal(x_sem);
        wait(next);
        next_count--;
    }
```

### Resuming Processes within a Monitor
- If several processes are queued on condition `x` and `x.signal()` runs, **which one resumes**? Plain **FCFS is often not adequate**.
- Solution: **conditional-wait** construct `x.wait(c)` where **c** = an integer **priority number**; the process with the **lowest** c (highest priority) is scheduled next.

### Example: Single Resource Allocation via Monitor
- Usage: `R.acquire(t); ... access resource ...; R.release();` where `t` = max time the process plans to use the resource (used as priority number — shortest requested time served first).
```
monitor ResourceAllocator {
    boolean busy;
    condition x;
    void acquire(int time) { if (busy) x.wait(time); busy = true; }
    void release()         { busy = false; x.signal(); }
    initialization code()  { busy = false; }
}
```
- **Incorrect usage patterns** (mirrors the semaphore misuse list): `release() … acquire()` (wrong order), `acquire() … acquire()` (double-acquire), **omitting** `acquire()` and/or `release()`.

## Liveness
- Processes may wait **indefinitely** trying to acquire a sync tool (mutex/semaphore) — this **violates Progress and Bounded Waiting**.
- **Liveness** = the set of properties a system must satisfy to ensure processes keep making progress.
- **Indefinite waiting is a liveness failure.**

### Deadlock
- Two or more processes waiting indefinitely for an event that only **one of the waiting processes** can cause.
- Example: S, Q semaphores both init to 1.
  - P0: `wait(S); wait(Q); ...; signal(S); signal(Q);`
  - P1: `wait(Q); wait(S); ...; signal(Q); signal(S);`
  - If P0 executes `wait(S)` and P1 executes `wait(Q)` first, each then blocks waiting for the resource the *other* holds → neither's `signal()` ever runs → **deadlock**.

### Other Liveness Failures
- **Starvation** — indefinite blocking; a process may never be removed from a semaphore's waiting queue (e.g. if the queue isn't FIFO / bad scheduling policy).
- **Priority Inversion** — a lower-priority process holds a lock needed by a higher-priority process, so a middle-priority process can run instead and effectively "invert" the intended priority order. **Solved via the priority-inheritance protocol** (the lock-holding low-priority process temporarily inherits the higher priority until it releases the lock).

> [!note]- Exam angle
> Three liveness-failure types — Deadlock, Starvation, Priority Inversion — with one distinguishing sentence each is a very likely short-answer set. Priority inversion's fix (priority-inheritance protocol) is often asked by name.
