# Multithreaded C Programming: Pthreads and OpenMP

## Executive Summary

This project explores parallel programming in C using **POSIX Threads (Pthreads)** and **OpenMP**. It covers everything from creating basic threads to dividing workloads, fixing race conditions, synchronizing shared memory, and analyzing overall performance[cite: 1].

We evaluated the exact same task ($1,000,000,000$ loop iterations) across three setups[cite: 1]:
1. **Sequential baseline** (1 CPU thread)[cite: 1]
2. **Pthreads** (scaling across thread counts)[cite: 1]
3. **OpenMP** (scaling across thread counts)[cite: 1]

### Key Results
* **Sequential Baseline:** $3.346414\text{ seconds}$[cite: 1]
* **Pthreads (16 threads):** $0.530415\text{ seconds}$ ($6.31\times$ speedup)[cite: 1]
* **OpenMP (16 threads):** $0.448971\text{ seconds}$ ($7.45\times$ speedup)[cite: 1]

While performance improved significantly with higher thread counts, speedup isn't perfectly linear due to background tasks like thread creation overhead, CPU scheduling, and memory access contention[cite: 1].

---

## Table of Contents

- [Project Objectives](#project-objectives)
- [How Parallelism Works](#how-parallelism-works)
- [Software Environment](#software-environment)
- [Core Concepts Made Simple](#core-concepts-made-simple)
- [Implemented Programs](#implemented-programs)
- [Project Directory Structure](#project-directory-structure)
- [Step-by-Step Execution Guide](#step-by-step-execution-guide)
- [Performance Analysis & Graphs](#performance-analysis--graphs)
  - [Execution Time](#execution-time)
  - [Speedup](#speedup)
  - [Efficiency](#efficiency)
- [Race Conditions & Solutions](#race-conditions--solutions)
- [Pthreads vs. OpenMP Comparison](#pthreads-vs-openmp-comparison)
- [Key Takeaways](#key-takeaways)
- [Conclusion](#conclusion)

---

## Project Objectives

* Learn how to split big jobs across multiple CPU threads.
* Manage low-level threads explicitly with **Pthreads**.
* Use compiler directives with **OpenMP** for automatic loop parallelization.
* Prevent data corruption (**race conditions**) on shared memory.
* Lock critical regions safely using **Mutexes** and **Critical Sections**.
* Safely accumulate results across threads using **Reductions**.
* Measure and calculate performance metrics: **Speedup** and **Efficiency**.

---

## How Parallelism Works

Instead of having one thread process a large job from start to finish, the workload is sliced into smaller pieces and assigned to multiple threads running at the same time.

```text
                     [ Main Workload ]
                             |
         +-------------------+-------------------+
         |                   |                   |
         v                   v                   v
    [Thread 1]          [Thread 2]          [Thread 3]
  (Slice 1 Data)      (Slice 2 Data)      (Slice 3 Data)
         |                   |                   |
         +-------------------+-------------------+
                             |
                             v
                     [ Final Output ]
```

---

## Software Environment

<img width="515" height="357" alt="image" src="https://github.com/user-attachments/assets/75a37642-e6a3-4af4-bae4-7ec0531abad9" />


---

## Core Concepts Made Simple

### 1. Pthreads (Explicit Control)
Pthreads gives you full hands-on control. You manually start threads, tell them what function to run, wait for them to finish, and clean up memory.
* `pthread_create()` — Spawns a new worker thread.
* `pthread_join()` — Pauses the main program until a thread finishes.
* `pthread_mutex_lock()` / `unlock()` — Creates a lock so only one thread can modify shared data at a time.

### 2. OpenMP (Automated Control)
OpenMP uses simple compiler rules (`#pragma` lines) added directly to normal code. The compiler automatically manages threads for you.
* `#pragma omp parallel for` — Automatically splits a `for` loop across available threads.
* `#pragma omp critical` — Ensures only one thread executes a block of code at a time.
* `reduction(+:sum)` — Let each thread calculate its own sum locally and automatically combines them into one final sum safely.

### 3. Race Condition & Synchronization
* **Race Condition:** Occurs when two threads try to change the same variable at the exact same moment (e.g., `counter++`), causing skipped updates or wrong output.
* **Synchronization:** A protective boundary (like a lock or mutex) that forces threads to wait their turn before touching shared data.

---

## Implemented Programs

### Part A — Pthreads Lab Exercises
* `thread1.c` — Create and run a single worker thread.
* `thread2.c` — Spawn and track multiple threads.
* `thread_sum.c` — Manually split math calculations across threads.
* `race.c` — Demonstrate data corruption from an unprotected shared variable.
* `mutex.c` — Fix data corruption using a Pthread mutex lock.
* `pthread_perf.c` — Measure runtime and scaling across thread counts.

### Part B — OpenMP Lab Exercises
* `omp1.c` — Basic parallel region and thread IDs.
* `omp_sum.c` — Automatic work distribution using `#pragma omp parallel for reduction`.
* `omp_race.c` — Demonstrate race conditions in OpenMP.
* `omp_critical.c` — Synchronize updates using `#pragma omp critical`.
* `omp_barrier.c` — Force threads to pause and wait for each other.
* `omp_perf.c` — Measure OpenMP performance across thread counts.

---

## Project Directory Structure

```text
parallel_lab/
│
├── pthreads/
│   ├── thread1.c
│   ├── multiple_threads.c
│   ├── thread_sum.c
│   ├── race.c
│   └── mutex.c
│
├── openmp/
│   ├── omp1.c
│   ├── omp_sum.c
│   ├── omp_race.c
│   ├── omp_critical.c
│   └── omp_barrier.c
│
└── performance/
    ├── sequential.c
    ├── pthread_perf.c
    └── omp_perf.c
```

---

## Step-by-Step Execution Guide

### Step 1: Open Terminal and Set Up Working Folder
```bash
wsl
mkdir -p ~/parallel_lab
cd ~/parallel_lab
```

### Step 2: Compile and Run Sequential Baseline
```bash
gcc sequential.c -o sequential
./sequential
```
> **Baseline Output:** $3.346414\text{ seconds}$[cite: 1]

### Step 3: Compile and Run Pthreads Benchmark
```bash
gcc pthread_perf.c -o pthread_perf -pthread
./pthread_perf
```
*(Enter thread counts when prompted: 1, 2, 4, 6, 16)*

### Step 4: Compile and Run OpenMP Benchmark
```bash
gcc omp_perf.c -o omp_perf -fopenmp
./omp_perf
```
*(Enter thread counts when prompted: 1, 2, 4, 6, 16)*

---

## Performance Analysis & Graphs

**Workload Size ($N$):** $1,000,000,000$ loop operations
**Expected Result:** `499999999500.00`

### 1. Execution Time (Lower is Better)

| Thread Count | Pthreads Time (s) | OpenMP Time (s) |
| :---: | :---: | :---: |
| **Sequential (1)** | **3.346414** | **3.346414** |
| **1** | 3.205682 | 3.186103 |
| **2** | 1.689293 | 1.662809 |
| **4** | 0.947512 | 0.978149 |
| **6** | 0.850071 | 0.841213 |
| **16** | 0.530415 | 0.448971 |

---

### 2. Speedup (Higher is Better)

$$\text{Speedup} = \frac{\text{Sequential Baseline Time}}{\text{Parallel Execution Time}}$$

| Threads | Pthreads Speedup | OpenMP Speedup |
| :---: | :---: | :---: |
| **1** | 1.044x | 1.050x |
| **2** | 1.981x | 2.013x |
| **4** | 3.532x | 3.421x |
| **6** | 3.937x | 3.978x |
| **16** | **6.309x** | **7.454x** |

---

### 3. Parallel Efficiency (Resource Utilization)

$$\text{Efficiency (\%)} = \left( \frac{\text{Speedup}}{\text{Number of Threads}} \right) \times 100$$

| Threads | Pthreads Efficiency | OpenMP Efficiency |
| :---: | :---: | :---: |
| **1** | 104.39% | 105.03% |
| **2** | 99.05% | 100.63% |
| **4** | 88.29% | 85.53% |
| **6** | 65.61% | 66.30% |
| **16** | **39.43%** | **46.58%** |

---

## Race Conditions & Solutions

### Unprotected Code (Buggy)
```c
// Dangerous: Multiple threads updating the same memory simultaneously
counter++;
```

### Pthread Fix (Mutex Lock)
```c
pthread_mutex_lock(&lock);
counter++;                  // Safe: Only 1 thread allowed at a time
pthread_mutex_unlock(&lock);
```

### OpenMP Fix (Critical Directive)
```c
#pragma omp critical
{
    counter++;              // Safe: OpenMP blocks concurrent updates
}
```

### OpenMP Best Practice (Reduction)
```c
#pragma omp parallel for reduction(+:sum)
for (long i = 0; i < N; i++) {
    sum += i; // Ultra-fast & safe: Local sums combined automatically
}
```

---

## Pthreads vs. OpenMP Comparison

| Feature | Pthreads | OpenMP |
|---|---|---|
| **API Level** | Low-level (Manual) | High-level (Directives) |
| **Thread Control** | Manual (`pthread_create`) | Automatic via directives |
| **Code Verbosity** | High (Requires extra setup) | Low (Few lines of `#pragma`) |
| **Synchronization** | Manual Mutexes | `#pragma omp critical` |
| **Summing Loops** | Manual division & local variables | `#pragma omp ... reduction()` |

---

## Key Takeaways

1. **Massive Speed Gains:** Running 16 threads cut execution time down from **3.34s** to **0.44s**[cite: 1].
2. **Efficiency Drops as Threads Increase:** Jumping to 16 threads drops resource efficiency under $50\%$ because thread management overhead takes up a larger portion of total execution time.
3. **Why Parallel Speedup Isn't Perfect:**
   * Time spent creating and shutting down threads.
   * CPU context switching and operating system scheduling.
   * Waiting time at synchronization checkpoints (locks/critical sections).
   * Memory bus saturation when multiple cores request data simultaneously.

---

## Conclusion

This project proves how multithreading dramatically accelerates CPU workloads in C[cite: 1]. **Pthreads** offers deep control over thread lifecycle management, while **OpenMP** delivers high performance with minimal code modifications. Proper synchronization through locks or reductions is crucial to achieving fast, accurate calculations without race conditions.
