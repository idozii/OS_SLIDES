# Scheduler Implementation

---

## Core Design Principles

- **MLQ**: Processes organized by priority levels
- **Round-Robin within Priorities**: Fair CPU time distribution
- **Priority-based Preemption**: High-priority processes run first
- **Time Slot Allocation**: `slot[i]=MAX_PRIO-i` for balanced execution
- **Multi-CPU Support**: Parallel execution for improved throughput

---

## Technical Challenges

- **Thread Safety**: Protected ready and running queues with mutex locks
- **Starvation Prevention**: Guaranteed execution slots for low-priority processes
- **Queue Management**: Optimized for O(1) enqueue/dequeue operations
- **Priority Inheritance**: Dual priority mechanism for flexible process management

---

## Demo Scenario

### sched_0 input

<pre style="text-align: center; font-family: monospace; line-height: 1.2; background-color: transparent; border: none; box-shadow: none; margin: 0 auto; display: block; width: auto;">
| Parameter           | Value | Description                            |
| ------------------- | ----- | -------------------------------------- |
| Time slice          | 2     | Each process executes for 2 time units |
| Number of CPUs      | 1     | Single processor system                |
| Number of processes | 2     | Total processes to be scheduled        |

| Process Information |          |              |                               |
| ------------------- | -------- | ------------ | ----------------------------- |
| **Start Time**      | **Name** | **Priority** | **Notes**                     |
| 0                   | s0       | 4            | Lowest priority, starts first |
| 4                   | s1       | 0            | High priority, arrives second |
</pre>

---

## Gantt Chart

<pre style="text-align: center; font-family: monospace; line-height: 1.2; background-color: transparent; border: none; box-shadow: none; margin: 0 auto; display: block; width: auto;">
Time: | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |10 |11 |12 |13 |14 |15 |16 |17 |18 |19 |20 |21 |22 |23 |
--------+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+
CPU 0 |   |s0 |s0 |s0 |   |s1 |s1 |s1 |s1 |s1 |s1 |s1 |s0 |s0 |s0 |s0 |s0 |s0 |s0 |s0 |s0 |s0 |s0 |s0 |
--------+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+
           ⌞────────⌟     ⌞---------------------------⌟⌞---------------------------------------------⌟
s0 starts       s1 runs (higher priority)               s0 completes execution
</pre>
