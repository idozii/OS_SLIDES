# Memory Management

---

## Architecture Overview

- **Virtual Memory Abstraction**: Processes see contiguous memory space
- **Page-based Organization**: Fixed-size pages mapped to physical frames
- **Demand Paging**: Pages loaded only when accessed
- **Page Replacement**: FIFO algorithm for victim selection

---

## Key Components

- **Page Tables**: Per-process mapping from virtual to physical addresses
- **Page Fault Handler**: Manages missing pages and initiates swapping
- **Swap Space Management**: Tracks available swap frames
- **Frame Allocation**: Bitmap-based tracking of physical memory

---

## How Swapping Works

<pre style="text-align: center; font-family: monospace; line-height: 1.2; background-color: transparent; border: none; box-shadow: none; margin: 0 auto; display: block; width: auto;">
┌────────────────┐      ┌────────────────┐      ┌────────────────┐
│   Page Fault   │      │  Victim Page   │      │ Get Swap Frame │
│   Detection    │─────▶│   Selection    │─────▶│                │
└────────────────┘      └────────────────┘      └────────┬───────┘
                                                 │
                                                 ▼
┌────────────────┐      ┌────────────────┐      ┌────────────────┐
│   FIFO Queue   │      │   Page Table   │      │  Data Transfer │
│   Management   │◀─────│    Updates     │◀─────│                │
└────────────────┘      └────────────────┘      └────────────────┘
</pre>

---

## Edge Case Handling

- **Swap Space Unavailability**: Direct frame reuse without swapping
- **Multiple Process Competition**: Fair allocation via FIFO queue
- **Memory Pressure**: Graceful degradation under high memory demand

---

## Test case

### os_1_mlq_paging input

<pre style="text-align: center; font-family: monospace; line-height: 1.2; background-color: transparent; border: none; box-shadow: none; margin: 0 auto; display: block; width: auto;">
| Parameter           | Value    | Description                               |
| ------------------- | -------- | ----------------------------------------- |
| Time slice          | 2        | Each process executes for 2 time units    |
| Number of CPUs      | 4        | Multi-processor system                    |
| Number of processes | 8        | Total processes to be scheduled           |
| Memory size         | 1048576  | Physical memory size in bytes             |
| Virtual memory size | 16777216 | Virtual address space size in bytes       |

| Process Information |          |              |                               |
| ------------------- | -------- | ------------ | ----------------------------- |
| **Start Time**      | **Name** | **Priority** | **Notes**                     |
| 1                   | p0s      | 130          | High priority process         |
| 2                   | s3       | 39           | Medium priority process       |
| 4                   | m1s      | 15           | Low priority process          |
| 6                   | s2       | 120          | High priority process         |
| 7                   | m0s      | 120          | High priority process         |
| 9                   | p1s      | 15           | Low priority process          |
| 11                  | s0       | 38           | Medium priority process       |
| 16                  | s1       | 0            | Highest priority process      |
</pre>

---

## Outputs

### Process Loading and Memory Allocation

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
Time slot   1
        Loaded a process at input/proc/p0s, PID: 1 PRIO: 130
        CPU 2: Dispatched process  1
Time slot   2
===== PHYSICAL MEMORY AFTER ALLOCATION =========================
PID=1 - Region=0 - Address=00000000 - Size=300 byte
print_pgtbl: 0 - 512
00000000: 80000001
00000004: 80000000
Page Number: 0 -> Frame Number: 1
Page Number: 1 -> Frame Number: 0
================================================================
</pre>

---

### Page Table Management

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
===== PHYSICAL MEMORY AFTER ALLOCATION =========================
PID=1 - Region=4 - Address=00000200 - Size=300 byte
print_pgtbl: 0 - 1024
00000000: 80000001
00000004: 80000000
00000008: 80000003
00000012: 80000002
Page Number: 0 -> Frame Number: 1
Page Number: 1 -> Frame Number: 0
Page Number: 2 -> Frame Number: 3
Page Number: 3 -> Frame Number: 2
================================================================
</pre>

---

### Memory Access Operations

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
pg_getpage: pgn=0
Write to memphy 276
===== PHYSICAL MEMORY AFTER WRITING ============================
write region=1 offset=20 value=100
print_pgtbl: 0 - 1024
00000000: 80000001
00000004: 80000000
00000008: 80000003
00000012: 80000002
Page Number: 0 -> Frame Number: 1
Page Number: 1 -> Frame Number: 0
Page Number: 2 -> Frame Number: 3
Page Number: 3 -> Frame Number: 2
================================================================
===== PHYSICAL MEMORY DUMP =====
BYTE 00000114: 100
</pre>

---

### Memory Deallocation

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
===== PHYSICAL MEMORY AFTER DEALLOCATION =======================
PID=3 - Region=0
print_pgtbl: 0 - 512
00000000: 80000005
00000004: 80000004
Page Number: 0 -> Frame Number: 5
Page Number: 1 -> Frame Number: 4
================================================================
</pre>

---

### Page Swapping

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
pg_getpage: pgn=3
Swap page 7 with 0
Write to memphy 2024
===== PHYSICAL MEMORY AFTER WRITING ============================
write region=2 offset=1000 value=1
print_pgtbl: 0 - 512
00000000: c0000000  # Note the high bit indicates swapped page
00000004: 80000006
Page Number: 0 -> Frame Number: 0
Page Number: 1 -> Frame Number: 6
================================================================
</pre>

---

### Memory Dump Showing Multiple Process Data

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
===== PHYSICAL MEMORY DUMP =========
BYTE 00000114: 103
BYTE 00000640: 102
BYTE 000007e8: 1
===== PHYSICAL MEMORY END-DUMP =====
</pre>
