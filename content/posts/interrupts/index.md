---
title: "Interrupts - Timers and Keyboard"
date: 2021-08-29
---

## Interrupts

Interrupts are essential to a computer functioning, they allow your keyboard and mouse to interrupt the CPU and tell it that an event is waiting. This means that when a program is running the kernel gets control of the CPU so that it can process the keyboard event. This also enables the CPU to tell us when we do something stupid such as dividing 0 by 0.

## Exceptions

To handle exceptions we must first program the IDT and GDT, checkout Phillip's tutorial for more on those. Then we can program handler function for the exceptions. To create a external function for the interrupts I used rust's FFI for `x86-interrupt`. This image below shows a double fault because we don't have a break-point handler.

![No breakpoint handler screenshot](no_breakpoint.png "No breakpoint handler screenshot")

To fix this we create a breakpoint handler which will show us where the code had a breakpoint, as well as where the stack is. This would allow us in the future to be able to change the stack and monkey patch code, for debugging purposes. An example breakpoint stack frame is shown below.

![Breakpoint handler screenshot](breakpoint.png "Breakpoint handler screenshot")
