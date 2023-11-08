- The system uses a log, as do most database systems, to recover metadata about the file system. 
- To gain performance, it uses ==group commit==, a concept derived from high performance database systems.

## Introduction:
File systems ought to provide :
1. good performance, 
2. rapid crash recovery, and 
3. robustness. 

This paper will discuss four implementation approaches used to achieve these goals:
1. log-based recovery for meta data
2. use of group commit to avoid some disk writes
3. design based on simple performance model that captures the timing characteristics of disks
4. techniques to increase the robustness of system

A file is a sequence of pages, logically numbered beginning with zero.

File package is concerned with allocating, deleting, opening, reading and writing file pages and keeping track of free pages.

File system keeps track of the names of all files in its file name table, which can be called a directory.

For the rest of the paper we will call the previous version of the file system CFS and call the reimplementation as FSD

## Requirements for FSD:
1. System should be robust against a hardware sector error
2. File system should be high performance
3. Robust against software errors
	- memory smashes
	- internal bugs
4. Fast recovery
5. Should use commercially available disk hardware

## Design Overview:
CFS splits the naming, property, and other information about files between three disk entities:
- file name table, 
- header sectors for files, and 
- labels for every disk sector

FSD moves all the header information from the file headers directly into the file name table. 

While CFS kept most critical information in two places, FSD keeps all its information in the file name table. This improves the locality of the file system. 

Redundancy is achieved in FSD by keeping two copies of the file name table; in CFS redundancy was achieved by keeping different data structures.

FSD also has a leader page for each file.

Changes to the file name table and leader pages are written to a log. The log is written such that there are two copies of all log records.

Group commit batches a set of updates together for a log write. the log is written every half second. This reduces the number of writes to the log.

FSD creates a one byte file by:
1. finding two pages from the VAM
2. update the file name table 
3. writing the leader page and data page


## Design Discussion:
The principle design concept is locality on the disk,

### File name table:
- The file name table maps a file name into a file.
- The run tables and file properties are kept in the file name table.
- To improve robustness, the file name table is written twice: every page is written on two different sectors with independent failure modes

### Leader Pages:
- Files in FSD consist of a single leader page and the data pages.
- Leader pages are a key element in the robustness of FSD
- Leader pages and the file name table are different data structures that are mutually checking. 

### Log-based recovery:
- Updates are applied to buffered copies of pages, but the copies are not forced to disk - they are just written to the log. 
- The writes of the buffers may be delayed (once logged) in anticipation of a further update to the page (a hot spot) or the writes may be done at a more convenient time.
  
- A log in FSD is used only for changes to the file name table and to the leader pages. It logs physical pages and is a redo log
- The log is kept as a circular disk file. New log records are appended to the log by synchronously writing the new record to the file
- There are potential problems in over writing data while it is still needed.
  
- The log is divided into thirds
- FSD records cache the identity of the third where the page was last logged.
- When the current log writes is about to enter a new third, there may be data logged in this new third that must be written to the disk
- Any pages logged in this new third but not logged in a later third are written to the file name table.
- Due to the high locality of the file name table, the number of name table pages normally written is nearly zero.
- The only times pages are written to the file name table are during entry into a new third and during crash recovery.
- This simple algorithm averages 5/6ths of the log in use.

### Group Commit:
A set of updates are grouped together in one log write to amortise the cost of the log write disk over several updates.

