# OS Scheduler

Core component for process management and execution

## Scheduler Design

### **Multi-level Queue Scheduling** with Round Robin

- Processes organized in multiple ready queues by priority
- Round-robin scheduling within each priority level

### Process Priority System

- Lower numerical values = Higher priority are scheduled first
- Ensures critical operations receive CPU time before less important ones

### Time Slot Allocation

- Each priority level receives dedicated time slots
- Allocation formula: `slot[i] = MAX_PRIO - i`
- Higher priority processes (lower i) receive more consecutive time slots
- Prevents starvation while maintaining priority

### Multi-CPU Support

- Multiple CPUs can run processes concurrently
- Improves overall system throughput and responsiveness
- Enables parallel execution across different priority levels

### Dual Priority Mechanism

- Process default priority defined in description file
- Can be overwritten by configuration in input file
- Input configuration priority takes precedence when both exist
- Provides flexibility without modifying process files

## Code Implementation

- Dual Mechanism:

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

-enqueue:

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

-dequeue:

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

-get_mlq_proc:

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

## Real Test & Interpretation

### sched_input

