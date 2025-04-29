# System Call

---

## Core System Calls

- **listsyscall**: Enumerates available system calls
- **killall**: Terminates all processes with a specified name
- **Memory I/O**: Controlled access to memory regions

---

## Process Termination Workflow

```markdown
┌───────────┐      ┌───────────┐      ┌───────────┐      ┌───────────┐
│ Read proc │      │ Search    │      │ Remove    │      │ Return    │
│ name      │─────▶│ queues    │─────▶│ matching  │─────▶│ count     │
└───────────┘      └───────────┘      └───────────┘      └───────────┘
```

---

## Protection Mechanisms

- **Memory Boundaries**: Prevents processes from accessing unauthorized memory
- **Parameter Validation**: Ensures system call arguments are valid
- **Error Handling**: Graceful recovery from invalid operations
