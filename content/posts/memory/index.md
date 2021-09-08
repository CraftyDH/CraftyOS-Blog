---
title: "Memory - Paging and Global Allocator"
date: 2021-09-06
---

## Memory

Memory is an essential component to a functioning OS, it allows for dynamic content such as strings and other data types which need to be stored on the heap. Therefore paging will first need to be implemented to be able to read and write to virtual memory locations which are stored at some other physical memory location. After this a Global Allocator which takes a page and builds a heap on it, for these purpose a linked list allocator will be used for allocations over 2 MiB, and a Fixed Size Allocator for smaller allocations allowing for fast allocations.

### Timeline
In this week the Paging and Global Allocator were quite easy to write due to the help of Phillop's tutorials help. As I have successfully completed all taskes set out in the [project's timeline](https://craftydh.github.io/CraftyOS-Blog/posts/restart/#revised-timeline), i am currently on track to begin work on Multitasking next week.

## Mapping
To handle paging I first needed to get the bootloader to pass a MemoryMap that was taken from the BIOS. This MemoryMap shows what is currently stored in every chunk of memory, such as hardware used memory or where the kernel was loaded. With this I wrote a mapper that tells the CPU the new mappings. An example where I wrote a string as 

```rust
// Map 0xDEADBEEF to 0xBEEF
let virtualAddr = Page::containing_address(VirtAddr::new(0xDEADBEEF));
let physicalAddr = PhysFrame::containing_address(PhysAddr::new(0xBEEF));
// Create the mapping
memory::create_mapping(virtualAddr, &mut mapper, physicalAddr, &mut  frame_allocator);

unsafe {
	// Write string to the raw pointer
	*(0xDEADBEEF  as  *mut  &str) =  "Storing this string at 0xDEADBEEF...";
	// Read string from the raw pointer
	println!("The string at 0xBEEF: {}", *(0xBEEF  as  *mut  &str));
}
```

When I run this is Qemu the expected output is that we can read the same value from the other address, and thats what we see!

![Mapping test screenshot](mapping.png "Mapping test screenshot")

