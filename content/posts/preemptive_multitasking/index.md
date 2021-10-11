---
title: "Preemptive Multitasking"
date: 2021-10-10
---

## Co-operative vs preemptive multitasking
In my OS I have already implemented co-operative multitasking however there are a number of reasons why I wanted to implement preemptive multitasking. Firstly since each task could run for an infinite amount of time it would be terrible for one rogue process to bring down the entire system. Secondly in order for tasks such as keyboard and mouse to run I had to add ```yield_now().await()```  all around my code base to share time. This made reading the code more challenging and it was still easy to accidentally add an expensive operation without a yield. Thirdly, in order to add multi processor functionality later on a proper process switcher would be needed and rewriting a code base for a different scheduler type would take forever.

##  Implementing preemptive multitasking
To implement the TaskManager I was able to copy over many of the tools I wrote for the co-operative scheduler however I actually found it easier to understand how it worked compared to rust async/await. This is likely because fundamentally process switching involves getting a process switch interrupt then saving the current process CPU state and restoring the new processes state.

The initialization of a process was challenging as I had to look up the [C calling convention](https://wiki.osdev.org/Calling_Conventions) in order to pass arguments. I so far only implemented the basic fact that you can pass 64 bit numbers or pointers in certain registers. I was then able to prefill the CPU state with the parameters instead of 0's when the task was created. Unfortunately this means only C safe types can be passed through this hacky solution, however I was able to ensure that the parameters passed were of the same type as the function requested via generic parameters as shown below.
```rust
impl Task {
    /// Safety for set_args_n
    /// As long as each type is not longer than 64 bits which would require 2
    pub fn set_args_3<A, B, C>(&mut self, func: extern "C" fn(A, B, C), a: A, b: B, c: C) {
		    // Set the instruction pointer to the function entry point
        self.state_isf.instruction_pointer = VirtAddr::from_ptr(func as *const usize);
        // Unsafely set the CPU registers to the parameters
        unsafe {
            self.state_reg.rdi = transmute_copy(&a);
            self.state_reg.rsi = transmute_copy(&b);
            self.state_reg.rdx = transmute_copy(&c);
        }
    }
}
```
The switching code was much simpler boiling down to save current state then get the next process and restore it's expected CPU state. A simplified code example is shown below.
```rust
impl TaskManager {
	/// Called by the interrupt handler
	pub fn switch_task(&mut self, stack_frame: &mut InterruptStackFrame, regs: &mut Registers) {
		let mut task_queue = self.task_queue.lock();
		// If we havn't instantiated any tasks return
		if self.tasks.is_empty() {
			return
		}
		// Ask the current task handler to save its stack_frame and CPU registers
		self.tasks.get_mut(&self.current_task).unwrap().save(stack_frame, regs);
		// Push old task to the back of the queue
		task_queue.push_back(self.current_task);

		// Can we get a new task
		if let Some(next_task_id) = task_queue.pop_front() {
			// Get the task from the tasks list
			let next_task = self.tasks.get(&next_task_id).unwrap();
			// Set the current task id to the new task we are executing
			self.current_task = next_task_id;

			unsafe {
				// Get a mutable pointer to the interrupt stack frame
				let isf = stack_frame.as_mut().extract_inner();
				// Write the new tasks isf to to CPU's isf
				write_volatile(
					isf as *mut InterruptStackFrameValue,
					next_task.stack_isf.clone()
				)
				// Set the CPU registers to the new processes state
				*regs = next_task.state_regs.clone();
			}
		}
	}
}			
```
