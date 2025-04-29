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

```markdown
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
```

---

## Edge Case Handling

- **Swap Space Unavailability**: Direct frame reuse without swapping
- **Multiple Process Competition**: Fair allocation via FIFO queue
- **Memory Pressure**: Graceful degradation under high memory demand
