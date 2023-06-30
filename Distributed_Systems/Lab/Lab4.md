### Part1:
- Make sure that when you choose the inumber for a new directory created with MKDIR, that inumber must have its most significant bit set to 0
- If your implementation passes the `test-lab-4-a.pl` script, you are done with part 1.
- Note that this is the first test that explicitly checks the correctness of these time attributes.


### Part2:
- Identify the RACE conditions
- `yfs_client` must use locks
- The testers for this part of the lab are `test-lab-4-b` and 
  `test-lab-4-c` (The source files are `test-lab-4-b.c` and `test-lab-4-c.c`)
- After you are done with part 2, you should also test with 
  `test-lab-4-a.pl`
- also test with `test-lab-4-b` with the same directory for both arguments

#### Detailed Guidance:
- Your best bet is to associate one lock with each file handle
- Use the file or directory's inumber as the name of the lock (i.e. pass the inumber to `acquire` and `release`)
- Our original template for the `yfs_client` constructor that we gave you in Lab 2 included the destination address of a lock server, so it should be very easy to add a `lock_client` object to the `yfs_client` and simply call its acquire and release methods.


Add lock client in the yfs client

We need to have a function that sets up the `struct stat` given a `fileinfo`

