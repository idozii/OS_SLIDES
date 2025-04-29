# Scheduler

---

## Scheduler Design

- **MLQ Scheduling and Round Robin**
- **Dual Priority Mechanism**
- **Multiple CPU support**

---

## Code Implementation

Dual Priority Mechanism:

```c
// In loader.c - Default priority assignment
#ifdef MLQ_SCHED
proc->prio = DEFAULT_PRIO;
#endif

// In os.c - Priority override during loading
struct pcb_t *proc = load(ld_processes.path[i]);
#ifdef MLQ_SCHED
proc->prio = ld_processes.prio[i]; // Override with configuration priority
#endif
```

---

### Queue Management

enqueue:

```c
void enqueue(struct queue_t *queue, struct pcb_t *proc) {
   // Verify valid queue and process
   if (queue == NULL || proc == NULL) return;

   // Check if queue is full
   if (queue->size == MAX_QUEUE_SIZE) {
      return;
   }

   // Add process to the end of the queue
   queue->proc[queue->size] = proc;
   queue->size++;
}
```

---

dequeue:

```c
// Remove and return first process from queue
struct pcb_t* dequeue(struct queue_t *queue) {
   if (queue == NULL || queue->size == 0) {
      return NULL;
   }

   struct pcb_t* proc = queue->proc[0];
#ifdef MLQ_SCHED
   // Shift remaining processes forward
   for (int i = 0; i < queue->size - 1; i++) {
      queue->proc[i] = queue->proc[i + 1];
   }

   queue->size--;
   return proc;
#else 
    ...
#endif
}
```

---

get_mlq_proc:

```c
struct pcb_t * get_mlq_proc(void) {
   struct pcb_t * proc = NULL;
   pthread_mutex_lock(&queue_lock);
   int i;
   
   // Core MLQ scheduling algorithm
   for (i = 0; i < MAX_PRIO; ++i) {
      // Skip empty queues or those that used up their slots
      if (empty(&mlq_ready_queue[i]) || slot[i] == 0) {
         // Reset slot count when depleted
         slot[i] = MAX_PRIO - i;
         continue;
      }
      
      // Get process from this priority level
      proc = dequeue(&mlq_ready_queue[i]);
      // Decrement remaining slots for this priority
      slot[i]--;
      break;
   }
   
   pthread_mutex_unlock(&queue_lock);
   return proc;
}
```

---

## Test Results

sched Test configuration:

```markdown
| Parameter           | Value | Description                            |
| ------------------- | ----- | -------------------------------------- |
| Time slice          | 4     | Each process executes for 4 time units |
| Number of CPUs      | 2     | Two processors running in parallel     |
| Number of processes | 3     | Total processes to be scheduled        |

| Process Information |          |              |                               |
| ------------------- | -------- | ------------ | ----------------------------- |
| **Start Time**      | **Name** | **Priority** | **Notes**                     |
| 0                   | p1s      | 1            | Medium priority, starts first |
| 1                   | p2s      | 0            | High priority, arrives second |
| 2                   | p3s      | 0            | High priority, arrives last   |
```

---

## Execution Timeline 1

```markdown
Time: | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |10 |11 |12 |13 |14 |15 |16 |17 |18 |19 |
-----+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+
CPU0 |   |   |P2 |P2 |P2 |P2 |P2 |P2 |P2 |P2 |P2 |P2 |P2 |P1 |P1 |P1 |P1 |P1 |P1 |   |
CPU1 |   |P1 |P1 |P1 |P1 |P3 |P3 |P3 |P3 |P3 |P3 |P3 |P3 |P3 |P3 |P3 |   |   |   |   |
-----+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+
```

---

sched_0 Test configuration:

```markdown
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
```

---

## Execution Timeline 2

```markdown
Time: | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |10 |11 |12 |13 |14 |15 |16 |17 |18 |19 |20 |21 |22 |23 |
--------+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+
CPU 0 |   |s0 |s0 |s0 |   |s1 |s1 |s1 |s1 |s1 |s1 |s1 |s0 |s0 |s0 |s0 |s0 |s0 |s0 |s0 |s0 |s0 |s0 |s0 |
--------+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+---+
```

---

sched_1 Test configuration:

```markdown
| Parameter           | Value | Description                            |
| ------------------- | ----- | -------------------------------------- |
| Time slice          | 2     | Each process executes for 2 time units |
| Number of CPUs      | 1     | Single processor system                |
| Number of processes | 4     | Total processes to be scheduled        |

| Process Information |          |              |                               |
| ------------------- | -------- | ------------ | ----------------------------- |
| **Start Time**      | **Name** | **Priority** | **Notes**                     |
| 0                   | s0       | 4            | Lowest priority, starts first |
| 4                   | s1       | 0            | Highest priority, arrives 2nd |
| 6                   | s2       | 0            | Highest priority, arrives 3rd |
| 7                   | s3       | 0            | Highest priority, arrives 4th |
```

---

## Execution Timeline 3

```markdown
Time: 0         5         10        15        20        25        30        35        40        45  
      ┌─────────┬─────────┬─────────┬─────────┬─────────┬─────────┬─────────┬─────────┬─────────┐
CPU 0 │    s0   │    s1   │         Round-robin scheduling between          │    s0   │         │
      │         │         │           s1, s2, and s3 (priority 0)           │         │         │
      └─────────┴─────────┴─────────────────────────────────────────────────┴─────────┴─────────┘
```
