Cache locks at client. 

when client 1 asks for lock 42 repeatedly and no other client wants the lock, then all acquire and releases can be performed on client 1 without having to contact the server.

Future considerations:
handlers on servers should run to completion without blocking.


Protocol Description:

On the client a lock can be in several states:
- _none_: client knows nothing about this lock
- _free_: client owns the lock and no thread has it
- _locked_: client owns the lock and a thread has it
- _acquiring_: the client is acquiring ownership
- _releasing_: the client is releasing ownership



A good way to implement releasing locks on the client is using a separate releaser thread. When receiving a revoke request, the client adds the revoke request to a list and wakes up the realeaser thread. The releaser thread will release the lock when the lock becomes free on the client. (==How do we let the releaser thread know that the lock has been released?==)

On the server, handlers shouldn't block either. A good way to implement this on the server is to have revoker and retrier threads that are in charge of sending `retry` and `revoke` RPCs, respectively.

RPCs can be out of order. Use a sequence number (random client id + sequence number).


Do not hold `pthread_mutexes` ==across== remote procedure calls. 


Pthreads mutexes are intended for synchronising multiple threads' access to shared memory. If you use them correctly, you will only need to hold the mutex for a few microseconds, and this in turn means that you could have a single pthreads mutex protecting the entire state of the lock server without limiting its scalability. This will make your job much simpler




- Client A releases a lock that client B wants. The server sends a `retry` RPC to client B. Then, before client B can send another `acquire` to the server, client C sends an `acquire` to the server. What happens in this case?
	Client C is given the lock
	Client B will send the acquire RPC
	Server responds with RETRY and add it to the retry queue


- If the server receives an `acquire` for a lock that is cached at another client, what is that handler going to do? Remember the handler shouldn't block or invoke RPCs.
	Server responds with RETRY and add it to the retry queue.
	Add the lock in the revoker queue
	invoke the revoker.


- If the server receives a `release` RPC, and there is an outstanding `acquire` from another client, what is the handler going to do? Remember again that the handler shouldn't block or invoke RPCs.

- If a thread on the client is holding a lock and a second thread calls `acquire()`, what happens? You shouldn't need to send an RPC to the server.
- How do you handle a `revoke` on a client when a thread on the client is holding the lock?
- If a thread on the client is holding a lock, and a second thread calls `acquire()`, but there has also been a `revoke` for this lock, what happens? Other clients trying to acquire the lock should have a fair chance of obtaining it; if two threads on the current node keep competing for the lock, the node should not hold the lock forever.
- How do you handle a `retry` showing up on the client before the response on the corresponding `acquire`?
- How do you handle a `revoke` showing up on the client before the response on the corresponding `acquire`?
- When do you increase a sequence number on the client? Do you have one sequence number per `lock_client` object or one per client machine?
- If the server grants the lock to a client and it has a number of other clients also interested in that lock, you may want to indicate in the reply to the `acquire` that the client should return the lock to the server as soon as the thread on the client calls `release()`. How would you keep track on the client that the thread's `release()` also results in the lock being returned to the server? (You could alternatively send a `revoke` RPC immediately after granting the lock in this case.)


Operations:

Server-Side
```
acquire_handler(lock_id, client_id) {
	if the lock does not exists: create the lock and the associated state
	
	if the lock free: 
		if client_id in retry_map[lock_id]: pop client_id
		send OK
	else:
		add the client in the retry_map[lock_id] 
		add the lock id in the revoker_queue and activate the revoker (CV?)
		send RETRY to the client
}

revoker() {
	while revoker_queue.size():
		lock_id = revoker_queue.pop()
		send revoke(owner[lock_id])
}

retryer() {
	while retry_queue:
		lock_id = revoker_queue.pop()
		for client in retry_map[lock_id]:
			send retry(client, lock_id)
}


release_handler(lock_id) {
	set the lock as free
	add the lock_id in the retry_queue and invoke the retrier
}
```


Server-Side States:
```
lock: pthread_mutex_t

lock_owners: map<lock_id, client_id>

retry_map: map<lock_id, set<client_id>>
retry_queue: queue<lock_id>
retry_queue_cv

revoke_queue: queue<lock_id>
revoke_queue_cv
```


Client-Side

```
revoke_handler(lock_id) {
	//TODO: examine the sequence number
	if state[lock_id] == 'ACQUIRING':
		return;
	state[lock_id] = 'RELEASING'
	if state is free then immediately release?
}

release(lock_id){
	if state[lock_id] == 'RELEASING' or 'ACQUIRING': 
		add lock_id in releaser_queue and invoke the releaser
	else:
		state[lock_id] = 'FREE'
}

acquire(lock_id) {
	if state[lock_id] == 'NONE':
		send acquire(lock_id)
	
	while state[lock_id] != 'FREE':
		cond_wait()
	state[lock_id] = 'LOCKED'
}

retry_handler(lock_id) {
	send signal to the thread that is conditional waiting for the locks cond
}

releaser() {
	for lock_id in release_queue:
		send release_RPC()
		broadcast(cv)
			- starts any thread that was waiting becuase lock was in releasing
			- starts any thread that was waiting because lock was in acquiring
}
```

Threads:
1. `revoker`: 
	For every lock in the revoke queue, send the revoke RPC to the client that holds that lock. The revoke RPC should also contain the lock id for which the revoke was sent.


Client Side States:
```
enum lock_state {NONE, LOCKED, FREE, RELEASING, ACQUIRING};
struct lock {
	lock_state,
	lock_id,
	lock_cv,
}
state: map<lock_ids, lock>

releaser_queue: queue<lock_id>
releaser_cv
```


Doubts:
- Should I only remove clients from the retry queue only when they actually acquire a lock?
	YES, we should only remove clients from the retry queue only when that client receives the lock.
	
	Do we store a list or a set of client_ids?
		What does it mean to have multiple client_ids in the retry? It means we have received multiple acquire request from that client for the same lock. Is this possible?
		If YES: use a queue
		If NO: use a set

- Duplicate Retry RPCs can be received even after acquiring the lock


- What to do when a revoke is received:
	- What if the lock was in ACQUIRING: due to delays




Problems:
 - Release request is not received on the server side
	 - even after receiving the revoke request the lock is not in RELEASING state


For SEQ#:
	There is always an existing sequence number for every type of rpc received. 

Clear the retry_present and revoke_present flags after the release.
	When do i clear the retry_present
	When do i clear the revoke_present