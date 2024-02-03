
| Team Member | Matriculation Number |
|-|-|
|Pranav Shetty | 7028806 |
|Jaideep More | 7043840 |

# 1. General Questions
## a
### 1.
Wrong.
- `strcpy` is a string copy function and it stops copying when it encounters a null byte (`\0`),
- `memcpy` does not interpret the data and copies a specified `n` number of bytes.

### 2.
Wrong.
- ROP doesn't necessarily require an executable stack.

### 3.
Wrong.
- The least significant byte of a multi-byte data type is stored at the lowest memory address

### 4.
Wrong.
- Memory tagging is a technique that can be used to protect against both spatial and temporal memory safety errors.

# 2. Return to libc
## a.
![[assignment3.png]]
## b.
- The 1st parameter of function `g` has the same offset as that of the 2nd parameter of function `f`, so only one parameter can be passed.
- There are no restrictions for function `g` as there are no overlapping parameters.

## c.
- The 1st parameter of function f and the return address of function g, both are at the same offset.

# 3. Defenses and Attacks
## a.

- The return address on the stack is `0x????????????0200` for `fn_a`
- we can overwrite the last byte with `0x40` to return to `fn_c` instead.
- We execute a simple buffer overflow attack with the following input

Input = 
padding of (
	20B for `buffer` + 
	8B for `rbp` 
) + (`0x????????????0240`) return address

## b.
The two gadget chains are:
$$B \rightarrow G \rightarrow H$$
$$B \rightarrow C \rightarrow A$$
