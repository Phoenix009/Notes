COW - Requires atomic write to change the pointers for disk blocks
persist logs on disk on commit and not the actual changes. These actual changes can be done as a group


-- How do we deal with multiple writes to the disk for the same objects?
What if failure before log flush?
What is the computer architecture for Cedar FS?
Group Commit is dependent on the underlying computer architecture


-- How to deal with really large logs?
WE cant overwrite the blocks unless the changes are persisted
Metadata

-- Are we solving both the above mentioned problems together? NO

-- Journalling FS
EXT4 Modes:
	1. Write Back 
	2. Full Journalling

-- UNDO logs

-- logging the before and the after image allows both undo and redo logs.


-- Storing before and after images allows individual transaction rollback in the case of group commit?

-- What should be the semantics for the commit? When do we be sure that the commit will guarantee the persistence?

