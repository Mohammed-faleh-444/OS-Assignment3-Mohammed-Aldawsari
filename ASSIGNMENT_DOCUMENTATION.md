# Assignment 3 - Complete Documentation

**Student Name**: [Mohammed Faleh Aldawsari]
**Student ID**: [444050971]
**Date Submitted**: [2026/5/2]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**:  [https://drive.google.com/drive/folders/1-03zEAatAKqe-n-HjYPp_vuBlaPlZwwI]

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

### Entry 1 - [Same Day: 4:30 PM]
What I implemented: Forked repository cloned project, set up environment in VS Code and updated my student ID in the code.
Challenges encountered: Understanding project structure and locating where to start editing.
How I solved it: Carefully read README file and navigated to SchedulerSimulationSync.java.
Testing approach: Compiled project to ensure setup was correct.
Time spent:30 minutes

---

### Entry 2 - [Same Day: 5:00 PM]
What I implemented: Added ReentrantLock for shared counters (contextSwitchCount, completedProcessCount, totalWaitingTime).
Challenges encountered: Understanding race conditions in increment operations.
How I solved it: Studied how multiple threads update shared variables and applied locking.
Testing approach: Ran program multiple times to verify consistent counter values.
Time spent:45 minutes

---

### Entry 3 - [Same Day: 5:45 PM]
What I implemented: Added ReentrantLock protection for execution log (ArrayList).
Challenges encountered: Risk of ConcurrentModificationException.
How I solved it: Added separate lock for log operations.
Testing approach: Verified execution logs without errors.
Time spent:30 minutes

---

### Entry 4 - [Same Day: 6:15 PM]
What I implemented: Implemented Semaphore for CPU scheduling control in run() and runToCompletion() methods.
Challenges encountered: Understanding acquire() and release() usage.
How I solved it: Followed producer-consumer concept and examples from lecture notes.
Testing approach: Confirmed only one process runs at a time.
Time spent:45 minutes

---

### Entry 5 - [Same Day: 6:45 PM – 7:00 PM]
What I implemented: Final testing, debugging, documentation completion, and preparation for video recording.
Challenges encountered: Ensuring all synchronization worked correctly without deadlocks.
How I solved it: Reviewed try-finally blocks and tested multiple executions.
Testing approach: Ran program multiple times successfully with consistent results.
Time spent:15 minutes

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

[Race conditions occur when multiple threads access shared data at the same time In this program
 contextSwitchCount, completedProcessCount, and totalWaitingTime are shared resources.
  Without synchronization multiple threads could update these variables simultaneously causing lost updates.
   For example two threads incrementing contextSwitchCount++ may overwrite each other resulting in incorrect counts.]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

[ReentrantLock is used for mutual exclusion (only one thread accesses critical section). 
Semaphore controls access based on permits (can allow multiple threads). In my code
 I used ReentrantLock for protecting shared counters and logs because they require strict mutual exclusion.
 I used Semaphore(1) to simulate CPU access ensuring only one process executes at a time.]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[Deadlock occurs when threads wait forever for locks held by each other. To prevent this
 I used try-finally blocks to ensure locks are always released.
 I also avoided nested locks and kept critical sections short
 This prevents threads from being stuck waiting indefinitely.]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[I used one shared lock (coarse-grained locking) for all three counters (contextSwitchCount, completedProcessCount, totalWaitingTime). I chose this because it is simpler to implement and ensures full consistency across shared metrics.
However, the trade-off is reduced concurrency since all counters are blocked even if unrelated. A better approach could be fine-grained locking (separate locks per counter), which allows multiple threads to update different counters simultaneously.
Since the counters are independent, fine-grained locking would provide better performance and higher concurrency. However, coarse-grained locking is safer and easier to debug.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: contextSwitchCount, completedProcessCount, totalWaitingTime

**Why they need protection**: They are updated by multiple threads simultaneously causing race conditions.

**Synchronization mechanism used**: ReentrantLock (counterLock)

**Code snippet**:
```java
counterLock.lock();
try {
    contextSwitchCount++;
} finally {
    counterLock.unlock();
}
```

**Justification**: Ensures atomic updates and prevents lost increments.

---

### Critical Section #2: Execution Log

**What resource**: executionLog (ArrayList)

**Why it needs protection**: ArrayList is not thread-safe and can crash during concurrent modification.

**Synchronization mechanism used**: ReentrantLock (logLock)

**Code snippet**:
```java
logLock.lock();
try {
    executionLog.add(message);
} finally {
    logLock.unlock();
}
```

**Justification**: Prevents ConcurrentModificationException.

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: Controls CPU access so only one process executes at a time.

**Number of permits and why**: 1 permit (binary semaphore) to simulate single CPU.

**Where implemented**: In run() and runToCompletion()

**Code snippet**:
```java
SharedResources.cpuSemaphore.acquire();
try {
    // execution
} finally {
    SharedResources.cpuSemaphore.release();
}
```

**Effect on program behavior**:Ensures sequential execution and prevents overlap.

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
javac SchedulerSimulationSync.java
java SchedulerSimulationSync
```

**Results**: Output values remained consistent across all runs.
(Show that running multiple times produces consistent, correct results)

**Why synchronization is necessary**: Without locks, counters would be inconsistent due to race conditions.
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)

**Conclusion**: Synchronization ensures deterministic results.

---

### Test 2: Exception Testing
**What I tested**: Checked for ConcurrentModificationException in logs.

**Testing procedure**: 

**Results**: No exceptions occurred.

**What this proves**: Execution log is properly synchronized.

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: Matching process completion and correct counters.

**Actual values**: Matched expected results in all runs.

**Analysis**: Locks ensured correct shared state updates.

---

### Test 4: Different Scenarios
**Scenario tested**: Multiple runs with different process counts.

**Purpose**: Verify stability under load.

**Results**: Program remained stable and correct.

**What I learned**:Synchronization is essential in all concurrent scenarios.

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[I learned that race conditions occur when multiple threads access shared data without control.
Using ReentrantLock helped me ensure only one thread modifies critical sections at a time.
Semaphore helped control CPU access and simulate real scheduling behavior.
I also learned that improper synchronization can lead to incorrect results or crashes.
This assignment improved my understanding of concurrency and thread safety.]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: Banking systems where multiple transactions update balances.

**Example 2**: Operating systems managing CPU scheduling and resource allocation.

---

### How I would explain synchronization to others:

[Synchronization is like a bathroom key system—only one person can use the bathroom at a time so others must wait.
Locks ensure only one thread accesses shared data at a time preventing conflicts.]

---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/Mohammed-faleh-444/OS-Assignment3-Mohammed-Aldawsari

**Number of commits**: 

**Commit messages**: 
1. set ID
2. add imports and locks and semaphore
3. protect counter
4. task2: add reentrantlock to protect execution log
5. task3: add semaphore to control concurrent CPU access
6. task 3: add semaphore control in runToCompletion()
7. documentation
8. video

---

## Summary

**Total time spent on assignment**: 7–8 hours

**Key takeaways**: 
1. Race conditions can corrupt shared data
2. Locks ensure safe access to critical sections
3. Semaphores control resource access

**Most challenging aspect**: Understanding synchronization logic

**What I'm most proud of**: Successfully preventing race conditions and making program stable

---

**End of Documentation**
