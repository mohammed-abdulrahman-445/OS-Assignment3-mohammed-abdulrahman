# Assignment 3 - Complete Documentation

**Student Name**: [Mohammed abdulrahman khal]  
**Student ID**: [445050228]  
**Date Submitted**: [30 april]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [Paste your personal Gmail Google Drive link here]

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [30 April, 7:01]
**What I implemented**:Add ReentrantLock to protect (contextSwitchCount,
completedProcessCount, totalWaitingTime) 

**Challenges encountered**: firs i dont now exactly to place lock() and unlock()

**How I solved it**: i used try finally blocks ensure that the lock is always released even if an error

**Testing approach**:I ran the program multiple times and became correct in every run

**Time spent**: 1 h 

---

### Entry 2 - [30 April, 7:07]
**What I implemented**: Add ReentrantLock (ArrayList - prevent ConcurrentModificationException)

**Challenges encountered**:I was unsure operations needed to be locked since multiple threads could access the list simultaneously

**How I solved it**: protect every  operation inside the logExecution() method using a lock,ensure that only one thread writes at a time

**Testing approach**: I ran the program with multiple processes and confirmed that no errors or crashes

**Time spent**: 1.5 h

---

### Entry 3 - [30 April, 7:11]
**What I implemented**: I implemented a Semaphore with 1 permit to control access

**Challenges encountered**: It was difficult to  understand the difference between using a Lock and a Semaphore

**How I solved it**: I used cpuSemaphore.acquire() before execution and release() after execution.

**Testing approach**: the execution and observed that processes run one at a time not simultaneously

**Time spent**: 1h

---

### Entry 4 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

### Entry 5 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

[In the original code, there are two race conditions.
in race condition 1
contextSwitchCount
Because multiple threads increment it at the same time and the increment operation  is not atomic
Some increments may be lost resulting in a lower than expected final value
in race condition 2
executionLog
Because ArrayList is not thread safe, and multiple threads may modify it simultaneously
It may cause a ConcurrentModificationException ]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

[The difference between ReentrantLock and Semaphore is that a ReentrantLock is used to protect critical sections by allowing only one thread at a time, while a Semaphore is used to control how many threads can access a shared resource.
In my code, I used ReentrantLock to protect shared variables such as contextSwitchCount completedProcessCount, totalWaitingTime, and the executionLog
I used a Semaphore to control access to the CPU by implementing cpuSemaphore with a 1 permit]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[A deadlock is a situation where multiple threads are permanently blocked
Two deadlock prevention techniques are
Using try-finally blocks to ensure that locks are always released
Avoiding nested locks or enforcing a consistent lock ordering]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[I used separate locks for each counter (fine-grained locking)
This is because the three variables (contextSwitchCount, completedProcessCount, and totalWaitingTime) are independent and do not share related data
This design allows multiple threads to update different counters simultaneously without blocking each other
The trade-off is that fine-grained locking is more complex to manage and  complexity
coarse-grained locking is easier but reduces performance due to higher contention.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
contextSwitchCount
completedProcessCount
totalWaitingTime


**Why they need protection**: 
These variables are shared among multiple threads and are updated concurrently, which can lead to race condition  

**Synchronization mechanism used**: ReentrantLock was used

**Code snippet**:
```java

public static final ReentrantLock contextSwitchLock = new ReentrantLock();
public static final ReentrantLock completedProcessLock = new ReentrantLock();
public static final ReentrantLock waitingTimeLock = new ReentrantLock();

public static void incrementContextSwitch() {
    contextSwitchLock.lock();
    try {
        contextSwitchCount++;
    } finally {
        contextSwitchLock.unlock();
    }
}

public static void incrementCompletedProcess() {
    completedProcessLock.lock();
    try {
        completedProcessCount++;
    } finally {
        completedProcessLock.unlock();
    }
}

public static void addWaitingTime(long time) {
    waitingTimeLock.lock();
    try {
        totalWaitingTime += time;
    } finally {
        waitingTimeLock.unlock();
    }
}
```

**Justification**: Using ReentrantLock ensures that only one thread can modify each variable at a time

---

### Critical Section #2: Execution Log

**What resource**: The resource is executionLog used by all threads to store execution logs

**Why it needs protection**: Because multiple threads may add elements to the list simultaneously is not thread-safe
This can lead to issues such as ConcurrentModificationException

**Synchronization mechanism used**: 
A ReentrantLock 

**Code snippet**:
```java
public static final ReentrantLock logLock = new ReentrantLock();
public static List<String> executionLog = new ArrayList<>();

public static void logExecution(String message) {
    logLock.lock();
    try {
        executionLog.add(message);
    } finally {
        logLock.unlock();
    }
}

```

**Justification**: Using ReentrantLock ensures that only one thread can modify the executionLog at a time

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: The primary purpose of the cpuSemaphore is to control concurrent access to the CPU

**Number of permits and why**: 1 (Binary Semaphore)
Since the simulation models a single-core CPU only one process can be handled at a time

**Where implemented**: Shared Definition
Usage Logic

**Code snippet**:
```java
// Definition in SharedResources class
public static final Semaphore cpuSemaphore = new Semaphore(1);

// Implementation in Process class run() method
public void run() {
    try {
        // Acquire the permit to use the CPU
        SharedResources.cpuSemaphore.acquire();
        try {
            // ... critical section: process execution logic ...
            SharedResources.incrementContextSwitch();
            // ... execution simulation ...
        } finally {
            // Always release the permit to prevent deadlock
            SharedResources.cpuSemaphore.release();
        }
    } catch (InterruptedException e) {
        System.out.println("Semaphore interrupted.");
    }
}

```

**Effect on program behavior**: The semaphore ensures that even if multiple threads are "ready," only one thread can enter the critical section and execute

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: I executed the following commands in the Visual Studio Code
```bash
# Command to compile the Java file
javac SchedulerSimulationSync.java

# Commands to run the simulation 5 consecutive times
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync

```

**Results**: 
In all 5 runs, the "Total Completed Processes" exactly matched the number of processes
The execution log size remained consistent, showing no missing entries or ConcurrentModificationException errors
No two processes ever printed  simultaneously
The Total Waiting Time was calculated correctly


**Why synchronization is necessary**: 
Synchronization is critical in this multithreaded environment to prevent Race Conditions


**Conclusion**: The testing confirms that the synchronization implementation in SchedulerSimulationSync.java is successful. The locks and semaphores effectively manage the shared resources,

---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: 
I focused on the logExecution method and the section of the code where multiple process threads attempt to write to the shared ArrayList simultaneously


**Results**: No Crashes
Log Integrity
Lock Performance


**What this proves**: proves that the Fine-Grained Locking strategy implemented in the SharedResources class is effective

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: Completed Processes:Must exactly equal the number of processes
Context Switches:Should be at least equal to the number of processes
Total Waiting Time:Must be the sum of each process's finish time


**Actual values**: 
Completed Processes:Match
Context Switches:Correctly incremented for every CPU burst entry
Total Waiting Time:Summed accurately without lost



**Analysis**: 
The alignment between expected and actual values proves that the ReentrantLock for counters and the cpuSemaphore are functioning perfectly

---

### Test 4: Different Scenarios
**Scenario tested**: Testing the simulation with a large number of processes and a very short time quantum


**Purpose**: To stress-test the synchronization mechanisms under high contention

**Results**: Performance Stability
Log Accuracy
Correct Calculations

**What I learned**: I learned that "Fine-Grained Locking" is highly efficient because it allows threads to update different variables

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[Race conditions:Synchronisation makes concurrent programs
predictable
Fine‑grained locking:protecting independent resources with separatelocks unlocks
Synchronisation adds overhead, but the safety
A binary Semaphore 1 is functionally similar
The
try‑finallypattern is non‑negotiable
finally
blockleads to deadlocks that are very hard]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: Flight Booking Systems

**Example 2**: Medical Imaging Devices

---

### How I would explain synchronization to others:

[The Lock (ReentrantLock) – The Bathroom Key
Think of a shared counter (like contextSwitchCount) as a bathroom. Only one person can have the key at a time.
A thread "locks" the variable, updates it, and then "unlocks" it so others can use it.
]

---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/mohammed-abdulrahman-445/OS-Assignment3-mohammed-abdulrahman.git

**Number of commits**: 29

**Commit messages**: 
1. Set my student ID: 445050228
2. add import for Semaphore and ReentrantLock
3. Add ReentrantLock to protect counter variables
4. Add ReentrantLock to protect execution log 

---

## Summary

**Total time spent on assignment**: 4 or 5 hour

**Key takeaways**: 
1. Learned how to apply ReentrantLock and Semaphore
2. Understood that shared resources like ArrayList
3. Realized the critical importance of using try-finally blocks

**Most challenging aspect**: The most challenging part was implementing "Fine-Grained Locking" correctly

**What I'm most proud of**: Mastery of Synchronization Tools

---

**End of Documentation**
