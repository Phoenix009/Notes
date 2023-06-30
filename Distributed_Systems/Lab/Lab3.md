Implement SETATTR, WRITE and READ in fuse.cc

The script tests each yfs_client sequentially so we do not need to worry about locking.


Implementing SETATTR:

See the FUSE lowlevel header file for necessary function specifications. 

`to-set` is a mask that tells which attributes should be set. 

We need to specifically pay attention to the size attribute

Two ways to go about this:
1. Do it on the server side. (Better IMO)
2. Do it on the client side. Requires a get and a put for the entire file. Why do this when we simply want to set a attribute (CURRENT)

Steps:
in yfs_client:
	1. get the attribute (getattr)
	2. if the file size needs to be modified
		1. Get the file (getfile)
		2. Modify the size (locally)
		3. Put the file (putfile)
	3. Put the attribute (setattr ==needed==)


Extent - attr:
- atime
- ctime
- mtime
- size




Implementing READ/WRITE:
Design decision: How do we want to store the files in the extent server. `std::string`? then what about storing large files?

Linux expects the file_system to return '\\0' for any reads of unwritten bytes.


Errors:
1. fileinfo constructor:
	possible inside std::stoi()
	
2. random characters:
	When using `memcpy` we are copying the `c_str` into the buffer. A modification was required to copy `min(content.size()-off, size)` characters.


