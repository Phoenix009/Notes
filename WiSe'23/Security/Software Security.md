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
