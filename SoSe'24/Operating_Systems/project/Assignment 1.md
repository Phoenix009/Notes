
# What is already implemented?
- thread creation
- thread completion
- simple scheduler to switch between threads
- synchronisation primitives
	- semaphore
	- locks
	- condition variables
	- optimisation barriers



the mechanics of the thread switch are in `threads/switch.s` x86 code that saves the state of the currently running thread and restores the state of the thread we are switching into


# What are we supposed implement?

## Alarm Clock
- https://souzanurafrianto.wordpress.com/2011/05/06/avoiding-busy-wait-in-timer_sleep-on-pintos/
## Producer Consumer Communication

## Priority Scheduling



# Overview

thread_init:
- init `tid_lock` which is used to assign thread ids in increment values
- init `ready_list`
- init `all_list`
- the current thread is executing `init.c/main` and is now given a proper thread structure


# unknown facts:

- Free memory starts at 1 MB and runs to the end of RAM.
- 