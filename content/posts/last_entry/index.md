---
title: "Last Entry for School Purposes"
date: 2021-10-31
---

## Final Timeline
This is the final timeline reflecting what tasks I actually completed. You can go to [original timeline](http://localhost:1313/CraftyOS-Blog/posts/restart/#revised-timeline) to see the difference which are quite minor.
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

### What features were changed?
Firstly USB was scrapped because the entire spec is over 600 pages and I didn't feel like wasting time on it. Secondly I didn't have enough time to create a functioning filesystem or a networking stack.

However, I instead implemented preemptive multitasking which allows greater flexibility with which tasks can run. It also paves the way into switching into user mode. Additionaly to help with that I implemented syscalls which allow processors to get a higher privilige thread (AKA the kernel) to complete a task.

## Reflection on the project
The OS has a lot of features packed into it. I think I just needed to work on a basic terminal with some commands so that all the features can be accessed and used. For now though multiple execuables can be used to still showcase how the OS works. I also learned a lot about Rust throughout the process with things such as lifetimes and dynamic dispatch. The reason I had to restart is because I made so many errors in the boot code I could simply not debug them. After this learning experience I think I will make a better edition from UEFI in the upcomming holiday period.

I feel like the fortnightly posts were better than these weekly ones. This is because I generally had tasks in other assignments that caused me to not work on it. I later found out that I could just say I had a "break" but for the most part I completed tasks earlier and made it's blog post the following week. 
