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
