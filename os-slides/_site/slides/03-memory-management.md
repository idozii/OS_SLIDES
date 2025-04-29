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
