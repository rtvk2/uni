# Chapter 3: Processes — Complete Notes

These notes have been cleaned for voice reading. Where the original contains code, a table, or a visual diagram, refer to the corresponding slide.

IPC sections highlighted as requested.

## Part 1: Process Concept (Slides 1–7)

### Slide 1: Title
Chapter 3: Processes — Silberschatz, Galvin, Gagne.

### Slide 2: Outline
1. Process Concept
2. Process Scheduling
3. Operations on Processes
4. Interprocess Communication (IPC)
5. IPC in Shared-Memory Systems
6. IPC in Message-Passing Systems
7. Examples of IPC Systems
8. Communication in Client-Server Systems

### Slide 3: Objectives
- Identify separate components of a process and illustrate how they are represented and scheduled.
- Describe how processes are created and terminated, including system calls.
- Describe and contrast interprocess communication using shared memory and message passing.
- Design programs that use pipes and POSIX shared memory for IPC.
- Describe client-server communication using sockets and remote procedure calls.
- Design kernel modules that interact with Linux.

### Slide 4: Process Concept
- An OS executes a variety of programs that run as a process.
- Process — a program in execution; process execution must progress in sequential fashion. No parallel execution of instructions of a single process.
- Multiple parts:
- Text section — the program code.
- Current activity — program counter, processor registers.
- Stack — temporary data (function parameters, return addresses, local variables).
- Data section — global variables.
- Heap — memory dynamically allocated during run time.

### Slide 5: Program vs Process
- Program is a passive entity stored on disk (executable file); process is active.
- Program becomes process when executable file is loaded into memory.
- Execution of program started via GUI mouse clicks, command line entry, etc.
- One program can be several processes.
- Consider multiple users executing the same program.

### Slide 6: Process in Memory
Refer to the diagram on Slide 6. It shows memory layout.

### Slide 7: Process in Memory — Code Mapping
Refer to the diagram on Slide 7.

## Part 2: Process States & PCB (Slides 8–12)

### Slide 8: Process States
As a process executes, it changes state:
- New — process is being created.
- Running — instructions are being executed.
- Waiting — process is waiting for some event to occur.
- Ready — process is waiting to be assigned to a processor.
- Terminated — process has finished execution.

### Slide 9: Diagram of Process State
Refer to the diagram on Slide 9. It shows transitions:
- New → Ready (admitted)
- Ready → Running (scheduler dispatch)
- Running → Ready (interrupt)
- Running → Waiting (I/O or event wait)
- Waiting → Ready (I/O or event completion)
- Running → Terminated (exit)

### Slide 10: Process Control Block (PCB)
Information associated with each process (also called task control block):
- Process state — running, waiting, etc.
- Program counter — location of instruction to next execute.
- CPU registers — contents of all process-centric registers.
- CPU scheduling information — priorities, scheduling queue pointers.
- Memory-management information — memory allocated to the process.
- Accounting information — CPU used, clock time elapsed since start, time limits.
- I/O status information — I/O devices allocated, list of open files.

Table:
- ---
- process number
- program counter
- registers
- memory limits
- list of open files
- ...

### Slide 11: Threads
- So far, process has a single thread of execution.
- Consider having multiple program counters per process.
- Multiple locations can execute at once.
- Multiple threads of control → threads.
- Must then have storage for thread details, multiple program counters in PCB.
- Explore in detail in Chapter 4.

### Slide 12: Linux task_struct
Represented by the C structure task(struct):
Refer to the code on Slide 12.

Diagram: Linked list of task(struct) with arrows, "current" pointing to currently executing process.

## Part 3: Process Scheduling (Slides 13–18)

### Slide 13: Process Scheduling
- Process scheduler selects among available processes for next execution on CPU core.
- Goal — maximize CPU use, quickly switch processes onto CPU core.
- Maintains scheduling queues of processes:
- Ready queue — set of all processes residing in main memory, ready and waiting to execute.
- Wait queues — set of processes waiting for an event (i.e., I/O).
- Processes migrate among the various queues.

### Slide 14: Ready and Wait Queues
Refer to the diagram on Slide 14. It shows ready queue and various wait queues (I/O, etc.).

### Slide 15: Representation of Process Scheduling
Refer to the diagram on Slide 15. It shows queues and transitions.

### Slide 16: Context Switch
A context switch occurs when the CPU switches from one process to another.

Refer to the diagram on Slide 16.

### Slide 17: Context Switch Details
- When CPU switches to another process, system must save state of old process and load saved state for new process via a context switch.
- Context of a process represented in the PCB.
- Context-switch time is pure overhead; system does no useful work while switching.
- The more complex the OS and PCB → the longer the context switch.
- Time dependent on hardware support.
- Some hardware provides multiple sets of registers per CPU → multiple contexts loaded at once.

### Slide 18: Mobile Systems
- Some mobile systems (e.g., early iOS) allow only one process to run, others suspended.
- Due to screen real estate, user interface limits.
- iOS provides:
- Single foreground process — controlled via user interface.
- Multiple background processes — in memory, running, but not on display, with limits.
- Limits include single, short task, receiving notification of events, specific long-running tasks like audio playback.
- Android runs foreground and background, with fewer limits.
- Background process uses a service to perform tasks.
- Service can keep running even if background process is suspended.
- Service has no user interface, small memory use.

## Part 4: Operations on Processes (Slides 19–28)

### Slide 19: Process Creation/Termination
System must provide mechanisms for:
- Process creation.
- Process termination.

### Slide 20: Process Creation
- Parent process creates children processes, which, in turn, create other processes, forming a tree of processes.
- Generally, process identified and managed via a process identifier (pid).
- Resource sharing options:
- Parent and children share all resources.
- Children share subset of parent's resources.
- Parent and child share no resources.
- Execution options:
- Parent and children execute concurrently.
- Parent waits until children terminate.

### Slide 21: Address Space & UNIX Examples
- Address space:
- Child duplicate of parent.
- Child has a program loaded into it.
- UNIX examples:
- fork() system call creates new process.
- exec() system call used after fork() to replace process memory space with a new program.
- Parent process calls wait() waiting for child to terminate.

Refer to the diagram on Slide 21.

### Slide 22: A Tree of Processes in Linux
Refer to the diagram on Slide 22. It shows process tree (e.g., init → login → shell → commands).

### Slide 23: UNIX fork/exec/wait Example
Refer to the code on Slide 23.

Trace:
- fork() creates child.
- Child: execlp replaces image with ls.
- Parent: wait() blocks until child exits.
- Parent prints "Child Complete".

### Slide 24: Windows CreateProcess Example
Refer to the code on Slide 24.

### Slide 25: Process Termination
- Process executes last statement and asks OS to delete it using exit() system call.
- Returns status data from child to parent (via wait()).
- Process' resources are deallocated by OS.
- Parent may terminate execution of children using abort() system call. Reasons:
- Child has exceeded allocated resources.
- Task assigned to child is no longer required.
- Parent is exiting, and OS does not allow child to continue if parent terminates.

### Slide 26: Cascading Termination, Zombies, Orphans
- Some OSes do not allow child to exist if parent has terminated. If a process terminates, all its children must also be terminated.
- Cascading termination — all children, grandchildren, etc., are terminated.
- Termination initiated by OS.
- Parent may wait for termination of child using wait() system call. Returns status information and pid of terminated process.
Refer to the code on Slide 26.

- If no parent waiting (did not invoke wait()) → process is a zombie.
- If parent terminated without invoking wait() → process is an orphan.

### Slide 27: Android Process Importance Hierarchy
Mobile OSes often terminate processes to reclaim resources. From most to least important:
1. Foreground process
2. Visible process
3. Service process
4. Background process
5. Empty process

Android will begin terminating processes that are least important.

### Slide 28: Chrome Browser Multiprocess
- Many web browsers ran as single process (some still do).
- If one web site causes trouble, entire browser can hang or crash.
- Google Chrome is multiprocess with 3 types:
- Browser process manages user interface, disk and network I/O.
- Renderer process renders web pages, deals with HTML, JavaScript. A new renderer created for each website opened.
- Runs in sandbox restricting disk and network I/O, minimizing effect of security exploits.
- Plug-in process for each type of plug-in.

Diagram: Each tab represents a separate process.

## Part 5: Interprocess Communication (IPC) — Overview (Slides 29–31)

⭐ IPC FOCUS STARTS HERE

### Slide 29: Cooperating Processes & IPC
- Processes within a system may be independent or cooperating.
- Cooperating process can affect or be affected by other processes, including sharing data.
- Reasons for cooperating processes:
- Information sharing.
- Computation speedup.
- Modularity.
- Convenience.
- Cooperating processes need interprocess communication (IPC).
- Two models of IPC:
1. Shared memory
2. Message passing

### Slide 30: IPC Models — Diagram

(a) Shared memory:
Refer to the diagram on Slide 30.

(b) Message passing:
Refer to the diagram on Slide 30.

Key contrast:
- Shared memory: kernel only involved in setup; faster after setup, but synchronization required.
- Message passing: kernel involved in every transfer; slower, but no synchronization issues.

### Slide 31: Producer-Consumer Problem
Paradigm for cooperating processes:
- Producer process produces information consumed by a consumer process.

Two variations:
1. Unbounded-buffer — no practical limit on buffer size.
- Producer never waits.
- Consumer waits if no buffer to consume.
2. Bounded-buffer — fixed buffer size.
- Producer must wait if all buffers full.
- Consumer waits if no buffer to consume.

## Part 6: IPC in Shared-Memory Systems (Slides 32–40)

⭐ IPC FOCUS

### Slide 32: Shared-Memory IPC
- An area of memory shared among processes that wish to communicate.
- Communication is under control of the user processes, not the OS.
- Major issue: provide mechanism to allow user processes to synchronize their actions when accessing shared memory.
- Synchronization discussed in Chapters 6 & 7.

### Slide 33: Shared Data — Buffer
Refer to the code on Slide 33.

Solution is correct, but can only use BUFFER(SIZE) - 1 elements.

### Slide 34: Producer Process (Shared Memory)
Refer to the code on Slide 34.

### Slide 35: Consumer Process (Shared Memory)
Refer to the code on Slide 35.

### Slide 36: Counter Solution — Fill All Buffers
- Suppose we want a solution that fills all buffers.
- Use integer counter keeping track of number of full buffers.
- Initially, counter = 0.
- Incremented by producer after producing.
- Decremented by consumer after consuming.

### Slide 37: Producer with Counter
Refer to the code on Slide 37.

### Slide 38: Consumer with Counter
Refer to the code on Slide 38.

### Slide 39: Race Condition Example
counter++ could be implemented as:
Refer to the code on Slide 39.

counter-- could be implemented as:
Refer to the code on Slide 39.

Interleaving with counter = 5 initially:
- S0: producer execute register1 = counter {register1 = 5}
- S1: producer execute register1 = register1 + 1 {register1 = 6}
- S2: consumer execute register2 = counter {register2 = 5}
- S3: consumer execute register2 = register2 - 1 {register2 = 4}
- S4: producer execute counter = register1 {counter = 6}
- S5: consumer execute counter = register2 {counter = 4}

Result: counter = 4 instead of 5 → race condition.

### Slide 40: Why No Race in First Solution?
- Question: Why was there no race condition in the first solution (where at most N-1 buffers can be filled)?
- Answer: More in Chapter 6.

## Part 7: IPC in Message-Passing Systems (Slides 41–50)

⭐ IPC FOCUS

### Slide 41: Message Passing
- Processes communicate without resorting to shared variables.
- IPC facility provides two operations:
- send(message)
- receive(message)
- Message size is either fixed or variable.

### Slide 42: Communication Link Requirements
If processes P and Q wish to communicate, they need to:
- Establish a communication link between them.
- Exchange messages via send/receive.

Implementation issues:
- How are links established?
- Can a link be associated with more than two processes?
- How many links can there be between every pair?
- What is the capacity of a link?
- Is the size of a message fixed or variable?
- Is a link unidirectional or bi-directional?

### Slide 43: Implementation of Communication Link
Physical:
- Shared memory
- Hardware bus
- Network

Logical:
- Direct or indirect
- Synchronous or asynchronous
- Automatic or explicit buffering

### Slide 44: Direct Communication
- Processes must name each other explicitly:
- send(P, message) — send a message to process P.
- receive(Q, message) — receive a message from process Q.
- Properties of communication link:
- Links are established automatically.
- A link is associated with exactly one pair of communicating processes.
- Between each pair there exists exactly one link.
- The link may be unidirectional, but is usually bi-directional.

### Slide 45: Indirect Communication
- Messages are directed and received from mailboxes (also referred to as ports).
- Each mailbox has a unique id.
- Processes can communicate only if they share a mailbox.
- Properties of communication link:
- Link established only if processes share a common mailbox.
- A link may be associated with many processes.
- Each pair of processes may share several communication links.
- Link may be unidirectional or bi-directional.

### Slide 46: Mailbox Operations
Operations:
- Create a new mailbox (port).
- Send and receive messages through mailbox.
- Delete a mailbox.

Primitives:
- send(A, message) — send a message to mailbox A.
- receive(A, message) — receive a message from mailbox A.

### Slide 47: Mailbox Sharing
- P1, P2, and P3 share mailbox A.
- P1 sends; P2 and P3 receive.
- Who gets the message?

Solutions:
- Allow a link to be associated with at most two processes.
- Allow only one process at a time to execute a receive operation.
- Allow the system to select arbitrarily the receiver. Sender is notified who the receiver was.

### Slide 48: Synchronization (Blocking vs Non-blocking)
Message passing may be either blocking or non-blocking.

- Blocking is considered synchronous:
- Blocking send — sender is blocked until message is received.
- Blocking receive — receiver is blocked until a message is available.

- Non-blocking is considered asynchronous:
- Non-blocking send — sender sends message and continues.
- Non-blocking receive — receiver receives a valid message, or null message.

- Different combinations possible.
- If both send and receive are blocking, we have a rendezvous.

### Slide 49: Producer-Consumer with Messages
Producer:
Refer to the code on Slide 49.

Consumer:
Refer to the code on Slide 49.

### Slide 50: Buffering
Queue of messages attached to the link. Implemented in one of three ways:

1. Zero capacity — no messages queued on a link.
- Sender must wait for receiver (rendezvous).
2. Bounded capacity — finite length of n messages.
- Sender must wait if link full.
3. Unbounded capacity — infinite length.
- Sender never waits.

## Part 8: Examples of IPC Systems (Slides 51–59)

⭐ IPC FOCUS

### Slide 51: POSIX Shared Memory
- Process first creates shared memory segment:
Refer to the code on Slide 51.

- Also used to open an existing segment.
- Set the size of the object:
Refer to the code on Slide 51.

- Use mmap() to memory-map a file pointer to the shared memory object.
- Reading and writing to shared memory is done by using the pointer returned by mmap().

### Slide 52: Blank/Artifact
- No content.

### Slide 53: POSIX Shared Memory — Consumer Code
Refer to the code on Slide 53.

### Slide 54: Mach Communication
- Mach communication is message based.
- Even system calls are messages.
- Each task gets two ports at creation — Kernel and Notify.
- Messages sent and received using mach(msg)() function.
- Ports needed for communication, created via mach(port)_allocate().
- Send and receive are flexible; four options if mailbox full:
- Wait indefinitely.
- Wait at most n milliseconds.
- Return immediately.
- Temporarily cache a message.

### Slide 55: Mach Code — Structures
Refer to the code on Slide 55.

### Slide 56: Mach Client Code
Refer to the code on Slide 56.

### Slide 57: Mach Server Code
Refer to the code on Slide 57.

### Slide 58: Windows LPC (Local Procedure Call)
- Message-passing centric via advanced local procedure call (LPC) facility.
- Only works between processes on the same system.
- Uses ports (like mailboxes) to establish and maintain communication channels.
- Communication works as follows:
- Client opens a handle to the subsystem's connection port object.
- Client sends a connection request.
- Server creates two private communication ports and returns handle to one to client.
- Client and server use corresponding port handle to send messages or callbacks and listen for replies.

### Slide 59: Local Procedure Calls in Windows — Diagram
Refer to the diagram on Slide 59. It shows client and server with ports.

## Part 9: Communication in Client-Server Systems (Slides 60–70)

⭐ IPC FOCUS

### Slide 60: Pipes
- Acts as a conduit allowing two processes to communicate.
- Issues:
- Is communication unidirectional or bidirectional?
- In case of two-way communication, is it half or full-duplex?
- Must there exist a relationship (i.e., parent-child) between communicating processes?
- Can pipes be used over a network?
- Ordinary pipes — cannot be accessed from outside the process that created it. Typically, a parent process creates a pipe and uses it to communicate with a child process it created.
- Named pipes — can be accessed without a parent-child relationship.

### Slide 61: Ordinary Pipes
- Allow communication in standard producer-consumer style.
- Producer writes to one end (write-end).
- Consumer reads from the other end (read-end).
- Ordinary pipes are therefore unidirectional.
- Require parent-child relationship between communicating processes.

Refer to the diagram on Slide 61.

Windows calls these anonymous pipes.

### Slide 62: Named Pipes
- Named pipes are more powerful than ordinary pipes.
- Communication is bidirectional.
- No parent-child relationship necessary between communicating processes.
- Several processes can use the named pipe for communication.
- Provided on both UNIX and Windows systems.

### Slide 63: Sockets and RPC
- Sockets.
- Remote Procedure Calls.

### Slide 64: Sockets
- A socket is defined as an endpoint for communication.
- Concatenation of IP address and port — a number included at start of message packet to differentiate network services on a host.
- The socket 161.25.19.8:1625 refers to port 1625 on host 161.25.19.8.
- Communication consists between a pair of sockets.
- All ports below 1024 are well known, used for standard services.
- Special IP address 127.0.0.1 (loopback) to refer to system on which process is running.

### Slide 65: Socket Communication
Refer to the diagram on Slide 65. It shows socket communication.

### Slide 66: Sockets in Java — DateServer
Refer to the code on Slide 66.

Trace:
- ServerSocket on port 6013.
- Infinite loop: accept connection, write date, close.

### Slide 67: Sockets in Java — DateClient
Refer to the code on Slide 67.

### Slide 68: Remote Procedure Calls (RPC)
- RPC abstracts procedure calls between processes on networked systems.
- Uses ports for service differentiation.
- Stubs — client-side proxy for actual procedure on server.
- Client-side stub locates server and marshalls parameters.
- Server-side stub receives message, unpacks marshalled parameters, performs procedure on server.
- On Windows, stub code compiled from specification written in Microsoft Interface Definition Language (MIDL).

### Slide 69: RPC — Data Representation & Failures
- Data representation handled via External Data Representation (XDR) format to account for different architectures.
- Big-endian and little-endian.
- Remote communication has more failure scenarios than local.
- Messages can be delivered exactly once rather than at most once.
- OS typically provides a rendezvous (or matchmaker) service to connect client and server.

### Slide 70: RPC Diagram
Flow:
1. Client calls kernel to send RPC message to procedure X.
2. Kernel sends message to matchmaker to find port number.
3. Matchmaker receives message, looks up answer.
4. Matchmaker replies to client with port P.
5. Kernel sends RPC to server.
6. Daemon listening to port P receives message.
7. Daemon processes request and sends output.
8. Kernel receives reply, passes it to user.

### Slide 71: End of Chapter 3

## Chapter 3 Summary — IPC Focus

- Topic: ---. Key Point: ---
- Topic: IPC Models. Key Point: Shared memory (fast, needs sync) vs Message passing (kernel-mediated)
- Topic: Shared Memory. Key Point: User-controlled; race conditions possible; POSIX shm(open), mmap
- Topic: Message Passing. Key Point: send()/receive(); direct or indirect (mailboxes)
- Topic: Synchronization. Key Point: Blocking (synchronous) vs Non-blocking (asynchronous)
- Topic: Buffering. Key Point: Zero (rendezvous), Bounded (wait if full), Unbounded (never waits)
- Topic: Pipes. Key Point: Ordinary (unidirectional, parent-child) vs Named (bidirectional, no relation)
- Topic: Sockets. Key Point: IP:port endpoint; Java example (DateServer/DateClient)
- Topic: RPC. Key Point: Stubs, marshalling, XDR, matchmaker

Mnemonics:
- IPC models: Shared Memory vs Message Passing → SM vs MP.
- Sync: Blocking = Synchronous; Non-blocking = Asynchronous.
- Buffering: 0 = rendezvous; Bounded = wait if full; Unbounded = never waits.
- Pipes: Ordinary = parent-child, unidirectional; Named = independent, bidirectional.

Common exam traps:
- Shared memory is faster after setup but requires synchronization.
- Message passing is slower but avoids race conditions.
- Ordinary pipes are unidirectional; named pipes are bidirectional.
- RPC uses stubs and marshalling.

## End of Chapter 3 Notes

This completes all slides in Chapter 3: Processes, with emphasis on Interprocess Communication.
