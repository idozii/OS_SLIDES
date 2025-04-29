# Memory Management

Virtual memory implementation with paging

---

## Overview

- Physical Memory consists of frames (RAM and SWAP)
- Virtual Memory organized into pages
- Address mapping translates between virtual and physical addresses

---

## Memory Operations

### Reading

The read_mem function handles memory reads:

```c
int read_mem(addr_t address, struct pcb_t * proc, BYTE * data) {
  addr_t physical_addr;
  if (translate(address, &physical_addr, proc)) {
    *data = _ram[physical_addr];
    return 0;
  } else {
    return 1;
  }
}
```

---

### Writing

The write_mem function handles memory writes:

```c
int write_mem(addr_t address, struct pcb_t * proc, BYTE data) {
  addr_t physical_addr;
  if (translate(address, &physical_addr, proc)) {
    _ram[physical_addr] = data;
    return 0;
  } else {
    return 1;
  }
}
```

---

### Others

libread: Reads data from a memory region
libwrite: Writes data to a memory region,
liballoc: Allocates memory for a process
libfree: Frees memory allocated to a process

---

## Calculating Frame Number