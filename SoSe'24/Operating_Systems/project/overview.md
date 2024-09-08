
Which thread states?

What is idle thread?

what is initial thread?

How are threads maintained?
	- in `ready_list` all the processes are in the `THREAD_READY` state
	- in `all_list` all the processes are stored. They are added when they are first created and removed when they exit

How to get the currently running thread?
- using `running_thread()`


How to create a new thread?
- using `thread_create`

How to initialise a thread?
- using `init_thread`
- we have to pass a thread struct, name and priority for the thread while initialising
- the newly created thread is in blocked state
- and still does not have a `thread_id`

- How are thread ids assigned?
	- using `allocate_tid`

How to register new interrupts handlers
- using `intr_register_ext`
