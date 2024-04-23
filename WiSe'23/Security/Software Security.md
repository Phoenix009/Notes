# OS Basics

## Process
- Process is an abstraction for a program in execution

- The OS maintains a lot of information about execution context.
	- Information related to all code and data needed for executing the program
	- State of CPU
	- List of open files
	- Related Processes 
	- etc
- **PCB- Process Control Block**:
	- All the attributes of a process are stored in a single structure called a PCB
	- The content of the PCB represents the context of a process

- **Representation**:
	- Stack: For local variables, function params, etc.
	- Heap: For dynamic memory allocation
	- Data Section: public and static variables
	- Code Section: stores the source code/instructions

- Processes allow for multi-processing
	- All processes get similar share of CPU resources
	- OS can enforce this using scheduling processes

- Processes can `fork` new child processes.
	- child processes inherit privileges, data, file descriptors and memory layout

## Threads
- Threads allow multiprocessing within a process

|What is not Shared?|What is Shared?|
|-|-|
|Thread ID|Code Section -> Instructions|
|Register Set|Data Section -> globals and static|
|Stack|OS Resources|
|Program Counter|Files|

Advantages:
- Faster Context Switch
- Resource Sharing
- Communication
- Utilisation


## Virtual Memory
- Enables to run larger programs with limited memory.
- Only the required pages are kept in the main memory others are brought in as and when required.
- Every process has its own virtual address space.
- Virtual addresses are mapped to physical addresses using the **page table**
- OS maintains a pointer to the page table. On x86 architecture: `CR3` Register

- **Demand Paging**:
	- brings the pages in the main memory only when the CPU demands
	- page fault occurs when the demanded page is not already present in the main memory
	- page fault service is called to handle page fault that brings the page from the secondary memory to the main memory.


# x64 Instruction Set
- Intel x86(32-bit) / x64(64-bit) follows Von-Neumann architecture (roughly speaking)

- CISC - Complete Instruction Set Computer
	- Multitude of instructions
	- various instruction formats
	- Variable instruction length encoding

## Intel Registers

![[intel-registers.png]]

General Purpose Registers for Intel x64/x86

|x64/x86|Purpose|
|-|-|
|rax/eax|Accumulator|
|rbx/ebx|Base Address for addressing|
|rcx/ecx|Counter (Loop Index)|
|rdx/edx|Data (I/O, `mul`/`div`)|
|rsi/esi|Source (String Source)|
|rdi/edi|Destination (String Destination)|
|rbp/ebp|Base Pointer (Frame Pointer)|
|rsp/esp|Stack Pointer (Top of Stack)|

Further Special Purpose Registers

|Register|Purpose|
|-|-|
|CS| Code Segment 16b|
|DS| Data Segment 16b|
|SS| Stack Segment 16b|
|ES/FS/GS| Further Segmentation Registers 16b|
|rip/eip| Instruction Pointer. Points to the next instruction|
|{R\|E} Flags| Flag Registers|

- Intel x64 adds 8 General purpose registers: r8 - r15
- Partial addressing can be used to address only specific bytes of these general purpose registers.

In this table are the least signifiant bits accessed or most significant bits accessed?

|Address|Size Accessed|
|-|-|
|r10**d**|last 32 bits|
|r10**w**|last 16 bits|
|r10**b**|last 8 bits|

## Intel Assembly
![[x64_cheatsheet (1).pdf]]


# Stack Organisation and Function Calls

- Stack is a LIFO Memory.
- `rsp` points to the top of stack

- Two basic assembly primitives:
	- `push src`: add to top of stack `Mem[--%rsp] = src` 
	- `pop dst`: remove top from stack `dst = Mem[%rsp++]`

## Calling a Function:

### Function Call:
- `call func`: `call` implicitly performs two operations.
	1. `push %rip`: Pushes the return address to stack
	2. `jmp func`: jump to the start of the specified function.
- To call a function, the program should place the first six integer or pointer parameters in the registers `%rdi`, `%rsi`, `%rdx`, `%rcx`, `%r8`, and `%r9`; 
- subsequent parameters (or parameters larger than 64 bits) should be pushed onto the stack, with the first argument topmost.
- The program should then execute the `call` instruction 

**Volatile and Non-Volatile Registers**
- `rbp`, `rbx`, `r12-r15`, `rsp` are non-volatile registers
- non-volatile registers are those that the caller can assume to not have modified after calling a function.
- the callee is responsible for saving and restoring the original values of these registers.
- all other registers are volatile and the caller is responsible for storing these registers if its using them.

### Function Return
- `ret` instruction implicitly performs these operations
	1. optional `remove <op>` bytes from stack
	2. restore return address from stack: `pop rip`
	3. change control flow back to caller: `jmp rip`
- If the function has a return value then it will be stored in `rax`


# Memory Layout: Stack in x64
![[function-call-stack.png]]

## Stack Frame
- Stack Frame is functions perspective on stack
- Starts with `%rsp` and local variables and ends at `%rbp`
- `%rsp`: Top of stack (stack pointer)
- `%rbp`: Start of current frame (base pointer)

## Setting up the Stack:
### Function Prologue:
- save old `%rbp` (stack frame): `push %rbp`
- establish new `%rbp` (stack frame): `mov %rbp, %rsp`
- reserve stack space for local vars: `sub %rsp, <size>`

### Function Epilogue:
- Restore old frame: `mov %rsp, %rbp`, `pop %rbp`
- Return to caller: `ret`

### Omit Frame Pointer:
- `%rbp` is not mandatory
- Addressing can be done relative to `%rsp`

## System calls using Linux x64 ABI
- Calling convention like normal function calls:
- the program should place the first six integer or pointer parameters in the registers `%rdi`, `%rsi`, `%rdx`, **`%r10`**, `%r8`, and `%r9`; 
- syscall number is placed in `%rax`
- Return value is in `%rax`

# User vs Kernel Mode
## Privilege Rings
- CPU enforces protection levels in hardware
- Protection levels provide a hardware enforced separation between privileged and less privileged code.

- Intel designed four rings: ring-0 most privileged to ring-3 least privileged
- Unprivileged processes ring in ring-3 (user mode). Device drivers **were planned** to run in ring-1 or ring-2. System software runs in ring-0 (kernel mode)

- In practice, OS's use just two rings kernel and user mode. No separation for device drivers, they run in kernel mode as well.


# Buffer Overflow:
- All of the data manipulated by machine instructions executed by the computer processor are stored in either the processors registers or in memory.
- There is nothing intrinsic in the registers or memory that indicates that some locations have an interpretation different from others.
- Thus, the responsibility is placed on the assembly language programmer to ensure that the correct interpretation is placed on any saved data values.
- C and its derivatives, success was due to its ability to access low-level machine resources while still having the expressiveness of high level control and data structures and because it was fairly easily ported to a wide range of processor architectures.
- Unfortunately, the ability to access low-level  machine resources means that the language is susceptible to inappropriate use of memory contents. 
- This was aggravated by the fact that many of the common and widely used library functions, especially those relating to input and processing of strings, failed to perform checks on the size of the buffers being used.

**Definition**:
- A condition at an interface under which more input can be placed into a buffer or data holding area than the capacity allocated, overwriting other information.
- Attackers can exploit such condition to crash a system or to insert a specially crafted code that allows them to gain control of the system.

- Occurs when a process attempts to store data beyond the limits of a fixed-size buffer and consequently overwrite adjacent memory locations.
- These locations could hold other program variables or parameters of program control flow data such as return addresses. 
- The consequence of this error include corruption of data, unexpected control transfer, possible memory access violations and eventually program termination.

- At its simplest, any unchecked copying of data into a buffer could result in corruption of adjacent memory locations, which may be other variables or possibly program control address and data.

- To exploit any type of buffer overflow, the attacker needs:
	1. to identify a buffer overflow vulnerability in some program that can be triggered using externally sourced data under the attackers control
	2. understand how that buffer will be stored in the processes memory and hence the potential for corrupting adjacent memory locations and potentially altering the flow of execution.

- Identifying vulnerable programs may be done by inspection of program source, tracing the execution of the programs as they process oversized inputs.



## Stack Buffer Overflow
- a stack buffer overflow occurs when the targeted buffer is located on the stack usually as a local variable in a functions stack frame.

![[buffer-overflow.png]]
- out of bounds write may corrupt data
- Some programming languages do not enforce strict bounds checks.

- Overwriting `%rip` enables hacker to run arbitrary code.
- Saved `%rip` will be blindly interpreted ad return address.
- Attacker can place arbitrary *shellcode* on stack and execute it.
- Shellcode is attacker-controlled code that will be jumped to and will execute after a memory corruption attack

- **The main intention is to get a shell inside the system.**
- We aim to use `sys_execve(fname, argv, envp)` system call

### NOP Sleds
- Sometimes attackers cannot predict the exact location of the shellcode
- We add `NOP` sled, at the beginning of the code
- No matter where entered, `NOP` sled will lead to the actual shellcode payload.
- Raises the change to start at the correct position

### Shellcode with Zero Bytes
- C string functions assume zero but `0x00` terminates a string
- If our shellcode contains 0x00 the string function will terminate taking the input and only partial shellcode will the taken as input
- Hence we should design shellcode to be zero-byte free. this guarantees that string functions do not terminate prematurely

|task|including zero bytes|zero-free|
|-|-|-|
|Zeroing a register|`mov rax 0`|`xor rax rax`|
|storing 1 byte|`mov rax 47`|`xor rax rax` <br> `mov al 47`|
|storing 3 byte|`xor rax rax` <br> `mov rax 0x112233`|`xor rax rax` <br> `mov ax 0x1122`<br> `shl eax 8`<br> `mov al 0x33`|


### Shellcode Minimization
- A buffer overflow may just allow to store a certain number of bytes
- Shellcode typically can be minimized by carefully selecting instructions


### ReverseShell:
- todo

### Tools for Software Exploitation & Shellcode writing

|Tool|Description|
|-|-|
|`readelf`|Display ELf file headers (sections, segments, symbols)|
|`nasm -f elf64 inp.asm -o bin`| Compile assembly; useful for shellcoders
|`objdump -d -j ".text" -MIntel bin`| Disassemble code section of program using Intel syntax |
|`gdb`/`gdbtui`|Standard Linux console debugger with assembly support|


## Buffer Overflow Defenses:

### Stack Canaries
- Stack canaries are a GCC compiler extension that inserts additional function entry and exit code.
- The added function entry code writes a canary value below the old frame pointer address, before the allocation of space for local variables.
- The added function exit code checks that the canary value has not changed before continuing with the usual function exit operations
- Any attempt at a classic stack buffer overflow would have to alter this value in order to change the old frame pointer and return addresses, and would thus be detected, resulting in the program being aborted.

#### Terminator Canaries
- Terminator canaries consist of bytes that terminate C-strings, ex: `0x00`, `'\n'`, `'\r'`, `0xFF`
- Attacker who aims to overwrite canary has to insert these bytes in the input
- Mitigates `rip` overwrite for several string functions


#### Limitations of Stack Canaries:
- Little security guarantees if random canary leaks
	- To mitigate this, compilers usually combine random with terminator canaries
	  
- No protection of local variables
	- Only `rbp` and `rip` are protected, local variables can still be overwritten
	  
- Only protects against stack-based buffer overflow
- Less protection for auto-forking process without re-randomisation

### Return Address XORing
- Similar idea to canaries: XOR return address with random value
- Even secure only if `rip` is overwritten in contrast to canary that assumed contiguous overwrite
- Susceptible to blind hacking as value is not re-randomised

### Non-Executable Stack
- A non-executable stack prevents classical shellcode attacks
- Basic principle: A page is wither writable (W) or executable (X) but never both (W+X).
- It is no longer possible to execute shell code placed on the stack.




# Format String Attacks:
- `printf` expects a format string as the first argument
- Values are consumed from the registers or stack
- Formatted string must not be in control of the attacker
- If so attacker can read almost arbitrary memory and even write to memory

# Code Reuse Attacks:
 