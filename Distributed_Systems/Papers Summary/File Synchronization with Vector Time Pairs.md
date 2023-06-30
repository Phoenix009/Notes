A vector time pair consists of two vector times: one tracking file modification history and one trancking file synchronization history.

### Problem Statement: Sychronization:

#### Synchronizing Files:
##### No Lost Updates:
If two replicas have different copies of a file it is safe to replace X with Y only if X's history is a prefix of Y. In this case we will say that Y is derived from X. 

If neither histroy is a prefix of the other, then replacing either copy with the other will lose updates. In such a situation, the synchronization reports a conflict.

##### Synchronizing Modifications:
We consider two versions different if they have different modification histories. Two files with identical versions will have same modification history and therefore identical content, however files having identical content may not have identical versions.

##### Recording Conflict Resolution:
Once conflicts have been detected and resolved, a file synchronizer should record the resolution so that if the same situation reoccurs, the user need not be bothered again.

##### Synchronizing Creation and Deletion:
Deletion is treated as just another simple change in the files modification history. However, always treating deletion like changes can result in unnecessary conflict. 

The intuition is that a ==deleted file should not conflict with an independently created file==. Reporting a conflict betwen two deleted files is unnecessary - the result is going to be a deleted file.

When a file is deleted, we assume that the metadata remains available for use in future synchronizations. In a systems where many files are short-lived, the cost of storing metadata for deleted files may be significant.




#### Synchronizing File Trees:
The amount of networok bandwidth consumed should be proportional to the amount of changed data, not the entire file tree

The synchronizer should support synchroization of sub trees and individual files.


#### Goals Revisited:
1. Impose no restrictions or requirements on the synchronization patterns between computers
2. Detect all conflicts without any false positives
3. Propagate file deletions without wasting space
4. Identify the set of files differeing between two computers using network bandwidth proportional to the size of the change
5. Support partial synchronizations restricted to subtrees of the file system.





### Vector Time Pair Algorithm:

#### Version Vector:
Version vectors keep enough information about file modification histories in order to compare two histories and apply the no-lost-update decision rule.

WE name a version by when and where it was created. We can simplify the history by noting that if $A_n$ is present in the history, then all distinct version of the form $A_m$ for $m<n$ must also be present in the history.

We can then summarize the history as a vector with one entry for each replica. $\{A \mapsto 5, B \mapsto 3\}$.

to compare histories represented by version vector u and v:
1. If the vectors are identical, so are the histories.
2. If $\forall i \ u_i \le v_i$ then u is a prefix of v

#### Vector Time Pairs:
A vector time pair augments the vector modification time with a ==vector synchronization time==. Just as the modification time summarizes the modification events in the life of a file, ==the vector synchronization time summarizes the synchronization events in the life of a file==.

We know nothing happens to a file between the files modification time and the synchronization time. 

Intuitively, the comparison $m_A \le m_B$  asks "does B's version have all the modification present in A's version?"; the comparision $m_A \le s_B$ asks "is B aware of all the changes present in A's version?"

Comparing the synchronization time from one replica with the modification time from a second replica checks whether the first replica knows about the modification events present on the second.


#### Single File Synchronization:
##### Recording Conflict Resolution:
##### Synchronizing Deletions:

#### Synchronizing File Trees:
