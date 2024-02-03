- We will first look at memory management and its pitfalls. 
- With these challenges, Rusts ownership model will be a crucial revelation
- Borrow Checker is a tool to learn from to write more dependable code

Working with memory appropriately was and is still today a challenging task. 

It is the software developers task to handle memory. Allocation memory when needed, read and write to appropriate memory and free memory when it is no longer needed.
## Memory Management Pitfalls

### 1. Uninitialised Memory
We are allowed to use a particular region of memory but it is not initialised. Ex: uninitialised variables.

### 2. Use After Free / Double Free
When we try to access a variable that has been freed. 

### 3. Buffer Over/Under-flow
When we try to access a memory location that is outside the allocated region. In memory safe languages this results in Out-of-Bounds error.

### 4. NULL Dereferences
If a program is the access a pointer, one would first need to check for `NULL` and depending on the result do the appropriate operation. 

This check however is not mandatory or enforced in many languages, this leads to an `NullReferenceException`. Tis can be particularly hard to debug

### 5. Data Races in Concurrent Access
No proper synchronisation while accessing shared resources.



## Ownership in Rust

### Ownership Model and Borrowing
In Rust, any piece of data is owned by exactly one owner. When the owner of the value goes out of scope the value is dropped.

If we only had single owners and they could not be changes for safety reasons, Rut would be rather limited in its functionality.

Rust allows four operations:
1. Ownership can be *moved*.
2. primitive types can be *copied*
3. It is possible to *borrow* a reference to a value
4. `stdlib` contains generic reference counted types.

### `move` vs `Clone` vs `Copy`
Ownership in rust can `move` from one owner to another. Rust does this by disallowing access to the previous owner after the `move`.

A type can implement the `Copy` trait.
- One can create a duplicate of the original value.
- `move` become `copy` implicitly
- The copy is a deepcopy

A type can implement the `Clone` trait
- Allows creating duplicates of types that cannot be copied bit-by-bit.
- A user can create a `clone` by using the `clone` function.
- Whether a clone is deep/shallow depends on the type

### Borrowing References
With `move`, `Clone` and `Copy`, we are moving and copying data around, which is not required for all cases. Particularly when the data we operate on is large and functions we want to use does not need ownership. For these cases rust provides **references**.

References represent non-owning pointer to data. they are of two types:
1. Shared references: provides read-only access
2. Exclusive reference: provides read-write access

At compile time, references are associated with *lifetime*. It is a concept in the compiler that tracks the portion of the program between a value being created and `drop`ed.

the following rules are enforces for references:
1. A reference cannot be created to null
2. no reference can outlive their referent
3. At a time there can either be an arbitrary number of shared references or a single exclusive reference
4. was long as there is an exclusive reference, the original owner cannot do anything with the data

