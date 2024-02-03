
| Team Member | Matriculation Number |
|-|-|
|Pranav Shetty | 7028806 |
|Jaideep More | 7043840 |


### 1. General Questions 
#### a. Process and Memory
##### 1. 
**Answer**: (c) They have their own virtual address space.

**Justification**: Processes have their own virtual address space, while threads share the address space with their parent process.

##### 2.
**Answer**: (c) It is easier to manage and hence needs less bookkeeping in the OS kernel.

**Justification**: More bookkeeping is required for mapping virtual to physical addresses.

##### 3. 
**Answer**: (b) A syscall is executed. and (d) A new process is created.

**Justification**: `fork()` is a syscall and a new process will be created.

##### 4. 
**Answer**: (a) The process was created using Copy-on-Write (CoW) and hence the first access was slower and (b) The process was interrupted during the time measurement of the first access.

**Justification**: 
(a): in CoW policy when write is performed on a page, its copy is first created and then the copy is modified. This causes a delay.
(b): the process can be interrupted to schedule another process,


#### b. Virtual Memory
##### 1. 
No changes will be observed. The address translation will translate the addresses to distinct physical addresses.

##### 2.
If both the virtual addresses are mapped to the same physical address then the changes will be observed.

##### 3.
With read and write access to the page table entry of page P1 attacker can possibly read and write all the memory addresses.


### 2. Intel x64 Assembly
#### a. Analysing Code
##### 1.
**Answer**: 5

##### 2.
**Answer**: 4080 (0xff0)

##### 3.
**Answer**: 65535 (0xffff)

#### b. Mixed Questions
##### 1. 
**Answer**: the difference between `LEA RAX, [ RAX + 1 ]` and `INC RAX` is that the latter changes `EFLAGS` but the former does not;

##### 2.
**Answer**: `ah` = `ch` = `al` < `bx` = `r15w` < `eax` < `rdx` 



#### c. Reverse Engineering
##### 1. 
the value in the `rax` is effectively added by `48` using a loop L1. (Lets call this new value `rax'`) and then we do a `syscall` with the `rax = 60` (sys_exit) with the return code given using `rdi = rax'`

##### 2.
The snippet calculates the factorial of the input number given in `rax` and stores the result in `rax`. If during this calculation the computed value overflows then we set `rax = -1` and return 



### 3. Control Flow Graph

![[Exercise1_cfg.drawio.png]]