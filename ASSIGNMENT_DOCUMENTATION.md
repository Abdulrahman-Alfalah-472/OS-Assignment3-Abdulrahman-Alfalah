# Assignment 3 - Complete Documentation

**Student Name**: [Abdulrahman Ramadan Alfalah]  
**Student ID**: [445052834]  
**Date Submitted**: [2nd May 2026]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [https://drive.google.com/file/d/1xD13QkO_PtwzylI6lVAWNq7q5YArGdXa/view?usp=drive_link]

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

### Entry 1 - [29 April, 9:00 PM]
**What I implemented**: I added a `ReentrantLock` to protect shared counters (`contextSwitchCount`, `completedProcessCount`, `totalWaitingTime`). I used lock with try-finally in each method.

**Challenges encountered**: At first, I was not sure where to put the lock and how to use it correctly

**How I solved it**: I used one lock inside the `SharedResources` class and wrapped each critical section with `lock.lock()` and `lock.unlock()` using try-finally.

**Testing approach**: I ran the program multiple times and checked that the counters always give correct values.

**Time spent**: 40 Minutes

---

### Entry 2 - [30 April, 11:00 PM]
**What I implemented**: I added a lock to protect the execution log. I used the same ReentrantLock and wrapped `executionLog.add()` with try-finally.

**Challenges encountered**: I was not sure if I should use a new lock or the same lock used for the counters.

**How I solved it**: I decided to use the same lock to keep the code simple.

**Testing approach**: I ran the program multiple times and checked that there are no errors.

**Time spent**: 20 Mins

---

### Entry 3 - [1 May, 2:00 PM]
**What I implemented**: I added a `Semaphore` with one permit to the SharedResources class, then I modified the `run()` method in the `Process` class to use `acquire()` before starting and `release()` after finishing the quantum.

**Challenges encountered**: I had to make sure the semaphore permit is released even if the process is interrupted so other processes can continue.

**How I solved it**: I put the `cpuSemaphore.release()` call inside the finally block.

**Testing approach**: I checked the terminal to see that only one process shows a progress bar at a time

**Time spent**: 40 Minutes

---

### Entry 4 - [1 May, 11:00 PM]
**What I implemented**: I completed the technical questions regarding race conditions and deadlocks, and the third part about synchronization analysis for the counters, the execution log, and the CPU semaphore.

**Challenges encountered**: Some new concepts were introduced like "Lock Granularity" so i had to understand it deeply

**How I solved it**: I used AI tools to understand the new concepts to be able to solve the tech questions.

**Testing approach**: -

**Time spent**: 3 Hours

---

### Entry 5 - [Date, Time]
**What I implemented**: I completed the remaining parts of the documentation including Part 4 (Testing), Part 5 (Reflection), and Part 6 (GitHub repository information). I also recorded and uploaded the required video demonstration to Google Drive.

**Challenges encountered**: Making sure the video meets all the requirements took some time.

**How I solved it**: Recorded the video multiple times to meet the needed requirements.

**Testing approach**: -

**Time spent**: 1 Hour

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

- In the original code, one race condition occurs with the `contextSwitchCount++` operation and the other counters incrementing operations. This is a shared variable, and the `++` operation is not atomic; it involves reading the value, incrementing it, and writing it back. If two threads perform this at once, one increment could be missing, leading to an incorrect total count.
- A second race condition exists in the `executionLog.add(message)` method. Since ArrayList is not thread-safe, concurrent access can cause a `ConcurrentModificationException`, resulting in a log that is missing entriesx.

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

A Lock is binary, meaning it is either locked or unlocked. It is used for mutual exclusion to ensure only one thread enters critical section at a time. A Semaphore can allow N threads at once based on its permit count. In my code, I used a `ReentrantLock` for the shared counters and the log because only one process should change those data at a time. I used a `Semaphore(1)` for the CPU to limit the simulation so that only one process can execute its quantum at any single moment.

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

A deadlock is a situation in which a set of processes are blocked because each process is holding a resource and waiting for another resource acquired by some other process, Two ways to prevent this are `try-finally` blocks and resource ordering. Resource ordering means that all processes must request resources in the exact same order. For example, if there are two resources, A and B, every process must take A first and then B. This prevents a circular wait where one process holds A and waits for B while another holds B and waits for A. In my code, I used `try-finally` blocks to prevent deadlocks. By putting the `unlock()` or `release()` call in the `finally` block, I ensured the resource is always freed even if an error occurs.

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

For Task 1, I chose coarse-grained locking by using one lock for all three counters. I made this choice because it is simpler to implement and easier to manage. The trade-off is that it can be slower because threads must wait for the same lock even if they are updating different counters. While I used a single lock for simplicity, a fine-grained approach (using a different lock for each counter) would provide better concurrency. This is because fine-grained locking allows multiple threads to update independent counters at the same time without blocking each other.

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: `contextSwitchCount`, `completedProcessCount`, and `totalWaitingTime`.

**Why they need protection**: Because they are shared by all process threads. Without protection, two threads might try to update a counter at the same time, causing lost updates.

**Synchronization mechanism used**: `ReentrantLock`

**Code snippet**:
```java
// Paste your implementation here
// Same implementaion for the other variables
public static void incrementContextSwitch() {
        lock.lock();
        try{
            contextSwitchCount++;
        } finally{
            lock.unlock();
        }
    }
```

**Justification**: The lock ensures that only one thread can modify the counters at any moment. i used the `try-finally` block to make sure that the lock is released even if an error occurs.

---

### Critical Section #2: Execution Log

**What resource**: `executionLog`

**Why it needs protection**: `ArrayList` is not thread-safe. If multiple processes try to add messages to the log at the same time, the updates could be wrong.

**Synchronization mechanism used**: `ReentrantLock`

**Code snippet**:
```java
// Paste your implementation here
public static void logExecution(String message) {
        lock.lock();
        try{
            executionLog.add(message);
        } finally{
            lock.unlock();
        }
    }
```

**Justification**: Using a lock around the `add()` method ensures that the execution history is recorded in the correct order and prevents missing updates.

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: To act as the CPU and manage which process is currently executing.

**Number of permits and why**: 1 permit. This ensures that only one process can use the CPU at a time.

**Where implemented**: Inside the `run()` method of the `Process` class.

**Code snippet**:
```java
// Paste your implementation here
try{
    SharedResources.cpuSemaphore.acquire();
    // The rest of the code...
    }finally {
            
            SharedResources.cpuSemaphore.release();
        }
     catch (InterruptedException e) {
        System.out.println(Colors.RED + "\n  ✗ " + name + " was interrupted while waiting for CPU." + Colors.RESET);}
```

**Effect on program behavior**: : It prevents multiple processes from printing their progress bars at the same time.

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
# Commands used (run the program at least 5 times)
Clicked on Run Main button 5 times.
```

**Results**: 
In every run, the results were the same: 10 completed processes, 17 context switches, and 34 log entries

**Why synchronization is necessary**: 
Synchronization is necessary because without it, race conditions could occur on the shared variables like counters (contextSwitchCounter) and the excutionLog(ArrayList), leading to incorrect results.

**Conclusion**: The use of locks and semaphores makes the program's output stable across multiple executions.

---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: I watched the program while multiple process threads were adding to the `ArrayList` used for the execution log

**Results**: The program completed all 10 processes without any exceptions.

**What this proves**: This proves that the `ReentrantLock` effectively manages access to the `ArrayList`.

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 
- Completed Processes: 10

- Context Switches: 17 (based on burst times and quantum).  

- Log Entries: 34 (Each context switch creates 2 entries: start and end).

**Actual values**: 
- Completed Processes: 10

- Context Switches: 17   

- Log Entries: 34

**Analysis**: The actual values match the expected values perfectly. This confirms that synchronization was implemented correctly

---

### Test 4: Different Scenarios
**Scenario tested**: Changing the Time Quantum from 4000ms to 2000ms.

**Purpose**: To test the synchronization under more context switches.

**Results**: The number of context switches increased, but the program remained stable

**What I learned**: The locks and semaphores continue to protect the shared data correctly even with larger number of prcoess and context swtiches if the synchronization logic is correct

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

Synchronization helps multiple threads use shared data safely without causing errors. I learned that without a lock, a race condition happens and important data is lost. I used a ReentrantLock to protect my counters and the execution log in my code. I also used a Semaphore to act like a CPU with only one permit for processes.

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: Banking: Synchronization prevents two people from withdrawing the same money from an account at the same time. This keeps the bank balance correct.

**Example 2**: Booking Seats. It makes sure two people cannot buy the same seat for a flight or a movie. It forces one person to finish before the next one starts.

---

### How I would explain synchronization to others:

Imagine a small bathroom with only one door and one lock. The lock is "synchronization." When one person goes inside, they lock the door so others must wait in line. If there was no lock, everyone would try to go in at once, and it would be a total mess. The bathroom here is the data and the lock is synchronization.

---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/Abdulrahman-Alfalah-472/OS-Assignment3-Abdulrahman-Alfalah

**Number of commits**: 11

**Commit messages**: 
1. Set my student id: 445052834
2. Implemented Task 1: Protected counter variables with locks
3. Implemented Task 2: Protected Excution log with a lock
4. Implemented Task 3: Contorlled CPU access using Semaphore

---

## Summary

**Total time spent on assignment**: 6 Hours

**Key takeaways**: 
1. Synchronization is essential to prevent race conditions
2. Semaphores can be used to control access to limited resources like CPU  
3. Locks provide mutual exclusion for protecting shared data  

    **Most challenging aspect**: The hardest part was knowing exactly where to use the locks and the semaphore without breaking the program. At first it was confusing how threads interact with shared data.

    **What I'm most proud of**:  I’m most proud that the program runs correctly every time without errors and all the results are consistent after adding synchronization.

---

**End of Documentation**
