---
title: "Last Entry for School Purposes"
date: 2021-10-31
---

## Final Timeline
1. Writing a rust BIOS application
   * VGA Text Mode
   * A testing suite
2. Exceptions and Interrupts
   * IDT / GDT
   * Exception Handlers
   * Timer Handler
   * Keyboard Handler
3. Paging / Memory
   * Map Physical Memory
   * Heap Allocator
4. Cooperative Multitasking
   * Futures
   * Async Keyboard
5. PCI
   * Show all PCI devices
6. Storage
   * Read/Write raw bytes to hard drive
7. Preemptive Multitasking
    * Using a timer interrupt to switch tasks.
8. Syscalls
    * ECHO
    * Yielding
    * Spawning new threads
9. Final Touches

## What features were changed?
Firstly USB was scrapped because the entire spec is over 600 pages and I didn't feel like wasting time on it. Secondly I didn't have enough time to create a functioning filesystem or a networking stack.

However, I instead implemented preemptive multitasking which allows greater flexibility with which tasks can run. It also paves the way into switching into user mode. Additionaly to help with that I implemented syscalls which allow processors to get a higher privilige thread (AKA the kernel) to complete a task.

