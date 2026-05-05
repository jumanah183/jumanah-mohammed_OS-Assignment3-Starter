# Assignment 3 - Complete Documentation

**Student Name**: [jumanah mohammed al-sharif]  
**Student ID**: [445052183]  
**Date Submitted**: [5/5/2026]

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

### Entry 1 - [Date, Time]
**What I implemented**: Defined the shared resources structure and identified critical sections. I set up the SharedResources class with counters for context switches, completed processes, and total waiting time.

**Challenges encountered**: Identifying which variables are susceptible to "Race Conditions" when multiple threads (processes) access them simultaneously.

**How I solved it**: ecided to use ReentrantLock for fine-grained locking. I created separate locks for each counter (e.g., contextSwitchLock, waitingTimeLock) to ensure that incrementing one counter doesn't block another, improving concurrency.

**Testing approach**: Print statements to check if the initial values were being initialized correctly.

**Time spent**: 1.5 Hours

---

### Entry 2 - [Date, Time]
**What I implemented**: Integrated Semaphore for CPU management and implemented the locking logic in the run() method of the Process class.

**Challenges encountered**: Ensuring that the semaphore is always released, even if a thread is interrupted or an error occurs. Failing to do this would lead to a "Deadlock" where the CPU remains permanently busy.

**How I solved it**: Used the try-finally block pattern. I placed the semaphore.acquire() outside the try block and semaphore.release() inside the finally block to guarantee the resource is freed.

**Testing approach**: Running the simulation with a small number of processes (3-5) to observe the "Acquire" and "Release" flow in the console.

**Time spent**: 2.5 Hours

---

### Entry 3 - [Date, Time]
**What I implemented**: Finalized the executionLog protection and added the "Run to Completion" logic for the last remaining process in the queue.

**Challenges encountered**: The ArrayList used for logging is not thread-safe, and concurrent access caused ConcurrentModificationException during the final summary.

**How I solved it**: Wrapped all interactions with the executionLog (especially the .add() method) inside a logLock.lock() block to ensure only one thread writes to the log at a time.

**Testing approach**: Stress testing the simulation with a large number of processes (20+) and a small time quantum to force frequent context switches and verify data consistency in the final statistics table.

**Time spent**: 1.5 Hours

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
In the original code, race conditions occur when multiple threads try to modify shared variables simultaneously.
1.	Shared Counters (contextSwitchCount & completedProcessCount):
• Shared Resource: Integer variables in the SharedResources class.
• Problem: The increment operation (e.g., count++) is not atomic; it involves three steps: read, increment, and write.
• Incorrect Behavior: If two threads execute this at once, they might both read the same initial value, resulting in one increment being "lost" (Lost Update).
• Code Example: contextSwitchCount++;

[Your answer here - 4-6 sentences with code examples]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:
In this implementation, I used both tools to handle different synchronization requirements:
• ReentrantLock (for Data Integrity):
• Where: Used in SharedResources to protect the counters and the log (e.g., contextSwitchLock, logLock).
• Why: I used locks because they provide Mutual Exclusion (Mutex). They are designed to ensure that only one thread can enter a "critical section" at a time, which is essential for protecting shared data from corruption during updates.
• Semaphore (for Resource Management):
• Where: Used cpuSemaphore in the Process class within the run() and runToCompletion() methods.
• Why: Semaphores are used for Signaling and Resource Counting. I used a semaphore with one permit to represent the CPU. It ensures that only one process is "permitted" to execute on the CPU at any given time. When a process finishes its quantum, it releases the permit so the next thread can acquire it.
[Your answer here - explain your implementation choices]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:
A deadlock is a situation where two or more threads are blocked forever, each waiting for a resource held by the other. To prevent this, two common techniques are used:
1.	Lock Ordering: Ensuring that all threads acquire locks in the same predefined order to avoid circular wait.
2.	Using Try-Finally Blocks: Ensuring that a lock is always released even if an exception occurs during execution.
In my code, I prevented deadlocks by strictly using the try-finally pattern. Every time a lock is acquired using lock(), the unlock() method is placed inside a finally block. This guarantees that the resource is never held indefinitely if the thread crashes or encounters an error. Additionally, since I used independent locks for independent resources, I avoided nested locking, which significantly reduces the risk of circular dependencies.
[Your answer here - reference try-finally blocks, lock ordering, etc.]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:
In my implementation, I chose to use separate locks for each counter (Fine-grained locking), such as contextSwitchLock, completedProcessLock, and waitingTimeLock.
I made this choice because the three counters are independent; incrementing the context switch count does not affect the total waiting time.
Trade-offs:
• Coarse-grained (One lock): It is simpler to implement and uses less memory, but it creates a "bottleneck" because threads must wait for each other even if they want to update different, unrelated variables.
• Fine-grained (Separate locks): It is slightly more complex but allows higher performance.
Conclusion:
The fine-grained approach provides better concurrency. Since the counters are independent, multiple threads can now update different counters simultaneously (e.g., one thread updates contextSwitchCount while another updates totalWaitingTime) without blocking each other. This maximizes CPU utilization and reduces thread contention compared to using a single global lock.
[Your answer here - explain coarse-grained vs fine-grained locking, independence of counters, concurrency implications. Show understanding of when to use each approach. 5-8 sentences expected.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: contextSwitchCount, completedProcessCount, and totalWaitingTime.

**Why they need protection**: 
These are shared global variables modified by multiple threads. Since operations like ++ or += are not atomic (read-modify-write), concurrent access leads to Race Conditions where updates can be lost, resulting in incorrect final statistics.
**Synchronization mechanism used**: ReentrantLock (specifically fine-grained locks like contextSwitchLock, completedProcessLock, and waitingTimeLock).

**Code snippet**:
```java
// contextSwitchLock.lock();
try {
    contextSwitchCount++;
} finally {
    contextSwitchLock.unlock();
}Paste your implementation here
```

**Justification**: 
I used ReentrantLock to ensure Mutual Exclusion. By using separate locks for each counter (fine-grained locking), I allow different threads to update different counters simultaneously, which improves performance while still guaranteeing data integrity.
---

### Critical Section #2: Execution Log

**What resource**: The executionLog (which is an ArrayList<String>).

**Why it needs protection**: 
The ArrayList class in Java is not thread-safe. If two threads attempt to add a message to the log at the same time, it can lead to structural corruption of the list (like ArrayIndexOutOfBoundsException) or data loss during the array's internal resizing.
**Synchronization mechanism used**: 
ReentrantLock (specifically logLock).
**Code snippet**:
```java
// logLock.lock();
try {
    executionLog.add(message);
} finally {
    logLock.unlock();
}Paste your implementation here
```

**Justification**: 
A lock is necessary here because adding to a list is a multi-step operation. The lock ensures that only one thread can modify the list structure at a time, preventing any corruption and ensuring every execution event is recorded correctly in the final output.
---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: The purpose is to act as a control mechanism that manages access to the CPU. It ensures that only a specific number of processes can be in the "running" state at the same time, simulating a real-world processor environment.

**Number of permits and why**: 1 permit.
I used a single permit because the simulation represents a single-core CPU. This ensures that only one process thread can execute its quantum or run to completion at any given time, preventing multiple processes from overlapping their execution outputs.

**Where implemented**: It is implemented in the Process class, specifically wrapping the logic inside the run() method and the runToCompletion() method.

**Code snippet**:
```java
// Pastry {
    SharedResources.cpuSemaphore.acquire();
    try {
        // ... execution logic ...
    } finally {
        SharedResources.cpuSemaphore.release();
    }
} catch (InterruptedException e) {
    // handle exception
}te your implementation here
```

**Effect on program behavior**: 
The semaphore forces threads to wait in a blocked state if the CPU (the permit) is currently held by another thread. This results in an orderly, sequential execution of processes, which eliminates interleaved console output and ensures that the timing of each process's execution is accurately simulated without interference.
---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results
Running the program multiple times with the same student ID (seed) to verify that the synchronization mechanisms (Locks & Semaphores) produce consistent, race-condition-free results across all executions.
**Testing procedure**: 
```bash
# # Compile the program
javac SchedulerSimulationSync.java

# Run the program 5 times to check for output consistency
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync Commands used (run the program at least 5 times)
```

**Results**: 
(Show that running multiple times produces consistent, correct results)
After 5 runs, all executions produced identical results for "Total Context Switches", "Total Completed Processes", and "Average Waiting Time". The execution log also contained the same number of entries every time. No ArrayIndexOutOfBoundsException or incorrect counter values were observed
**Why synchronization is necessary**: 
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)
Without synchronization, multiple threads would attempt to update shared resources (like contextSwitchCount and executionLog) simultaneously. This could lead to:
1.	Lost Updates: Two threads incrementing a counter at once might result in only one increment being saved.
2.	Memory Corruption: Concurrent access to the ArrayList (log) could cause the program to crash or lose log data.
3.	Overlapping Execution: Without the CPU semaphore, multiple processes might print to the console at the exact same time, making the output unreadable and logically incorrect for a single-core simulation.


**Conclusion**: 
The implementation of ReentrantLock and Semaphore successfully prevented race conditions. The program is now thread-safe, providing reliable and deterministic statistics regardless of how many times it is executed.
---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: I ran the simulation with multiple threads (processes) all attempting to write to the executionLog (ArrayList) and update shared counters simultaneously without the synchronization locks, then I re-ran it with the ReentrantLock implementation active.

**Results**: 
With locks disabled, the program occasionally crashed or skipped log entries. With locks enabled, the simulation completed smoothly every time with 0 exceptions, regardless of the number of concurrent processes.
**What this proves**: 
This proves that the logLock successfully provides mutual exclusion, preventing structural corruption of the ArrayList and ensuring thread safety during concurrent write operations
---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: Completed Processes: Should exactly match the initial numProcesses (from the header).
 Context Switches: Should be at least equal to the number of processes (or more if time quantum was exceeded).
 Average Waiting Time: Should be a positive value calculated as: (Total Waiting Time / numProcesses).

**Actual values**: Student ID 445052183
* Total Completed Processes: 10 to 20 (depending on the random seed).
* Total Context Switches: Matches the log entries.
* Average Waiting Time: Calculated correctly by the printStatistics method.

**Analysis**: 
The actual values perfectly match the expected logical outcomes. The "Total Completed Processes" in the final statistics matches the number of processes started, and the "Execution Log Summary" shows a complete history without data loss, confirming that the synchronization mechanisms (Locks and Semaphores) preserved the integrity of the shared data.
---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]
Increasing the number of processes (e.g., 20+ processes) and using a very small Time Quantum.
**Purpose**: To observe how the system handles high contention for the shared CPU resource and whether the synchronization overhead affects the stability of the counters.

**Results**: The simulation took longer to complete due to frequent context switching. However, despite the high "traffic" of threads trying to access the shared resources, the final completedProcessCount and executionLog remained perfectly accurate and consistent.

**What I learned**: I learned that while synchronization ensures data integrity, having too many context switches (high contention) can impact performance. However, the use of fine-grained ReentrantLock helped in maintaining concurrency without bottlenecking the entire system.

---

## Part 5: Reflection and Learning

### What I learned about synchronization:
Through this assignment, I gained a deep understanding of how multiple threads interact within an operating system. I learned that even a simple operation like incrementing a counter is unsafe without synchronization due to its non-atomic nature. Dealing with Race Conditions taught me the importance of identifying "Critical Sections" before writing code. I now understand the practical difference between Semaphores, which I used to manage CPU access like a signaling system, and Locks, which I used for data protection. The biggest challenge was ensuring that every lock() has a corresponding unlock() in a finally block to prevent deadlocks. Overall, I realized that synchronization is the backbone of any stable multi-threaded application.
[6-8 sentences about key concepts, challenges, insights]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: Online Banking Systems: When two different transactions (threads) try to withdraw money from the same account simultaneously, synchronization is critical to ensure the balance is updated correctly and doesn't allow withdrawing more than the available limit.

**Example 2**: E-commerce Inventory Management: When multiple customers are buying the last item in stock at the exact same millisecond, synchronization ensures the stock count is decremented accurately and the item isn't oversold to more people than available.

---

### How I would explain synchronization to others:

[Explain to someone who just finished Assignment 1 - use simple terms and analogies]
Imagine Assignment 1 was like a single chef in a kitchen; everything happened in order, and there was no confusion. In this assignment, we added multiple chefs (Threads) working in the same kitchen at the same time.
Synchronization is like having rules or a "talking stick" in the kitchen. Without it, two chefs might try to grab the same knife or salt the same pot twice, ruining the meal (Race Condition). Using Locks is like a bathroom key; if you have the key, you are the only one who can enter. Semaphores are like a limited number of seats in a restaurant; only a certain number of people can sit and eat at once. It’s all about making sure that when multiple things happen at once, they don't interfere with each other and corrupt our data.
---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/jumanah183/jumanah-mohammed_OS-Assignment3-Starter/edit/main/ASSIGNMENT_DOCUMENTATION.md

**Number of commits**: 

**Commit messages**: 
1. 
2. 
3. 
4. 

---

## Summary

**Total time spent on assignment**: 

**Key takeaways**: 
1. 
2. 
3. 

**Most challenging aspect**: 

**What I'm most proud of**: 

---

**End of Documentation**
