# System Call

---

## Core System Calls

- **listsyscall**: Enumerates available system calls
- **killall**: Terminates all processes with a specified name
- **Memory I/O**: Controlled access to memory regions

---

## Process Termination Workflow

<pre style="text-align: center; font-family: monospace; line-height: 1.2; background-color: transparent; border: none; box-shadow: none; margin: 0 auto; display: block; width: auto;">
┌───────────┐      ┌───────────┐      ┌───────────┐      ┌───────────┐
│ Read proc │      │ Search    │      │ Remove    │      │ Return    │
│ name      │─────▶│ queues    │─────▶│ matching  │─────▶│ count     │
└───────────┘      └───────────┘      └───────────┘      └───────────┘
</pre>

---

## Protection Mechanisms

- **Memory Boundaries**: Prevents processes from accessing unauthorized memory
- **Parameter Validation**: Ensures system call arguments are valid
- **Error Handling**: Graceful recovery from invalid operations

---

## Test case

### os_syscall input

<pre style="text-align: center; font-family: monospace; line-height: 1.2; background-color: transparent; border: none; box-shadow: none; margin: 0 auto; display: block; width: auto;">
| Parameter           | Value    | Description                               |
| ------------------- | -------- | ----------------------------------------- |
| Time slice          | 2        | Each process executes for 2 time units    |
| Number of CPUs      | 1        | Single processor system                   |
| Number of processes | 2        | Total processes to be scheduled           |
| Memory size         | 2048     | Physical memory size in bytes             |
| Virtual memory size | 16777216 | Virtual address space size in bytes       |

| Process Information |          |              |                               |
| ------------------- | -------- | ------------ | ----------------------------- |
| **Start Time**      | **Name** | **Priority** | **Notes**                     |
| 9                   | sc2      | 15           | System call demonstration     |
| 10                  | P0       | 15           | Target process for syscall    |
</pre>

---

## Outputs

### Process Termination Syscall

<pre style="text-align: left; 
            font-family: 'Consolas', 'Monaco', monospace; 
            line-height: 1.5; 
            background-color: #2b2b2b; 
            color: #e6e6e6; 
            border-radius: 5px; 
            padding: 15px; 
            margin: 10px 0; 
            font-size: 18px; 
            overflow: auto; 
            white-space: pre;
            box-shadow: 0 3px 6px rgba(0,0,0,0.16);">
The procname retrieved from memregionid 1 is "input/proc/P0"
Total of 4 processes named 'input/proc/P0' terminated
Time slot  19
CPU 0: Processed 1 has finished
CPU 0 stopped
</pre>
