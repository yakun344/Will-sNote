## Atomicity and semaphores
_update Oct 22, 2017  15:19_

---
#### Kinds of atomicity
**synthetic (voluntary) atomicity**: programs and/or threads obey locking to avoid operating at the same time.   
**kernel atomicity**: when a system call is executing, the scheduler won't interrupt it.   
**instruction atomicity**: some machine instructions preclude concurrent operations.  
**special-purpose system calls**: some specific system calls execute program instructions in an atomic (uninterruptible) fashion.  

#### Kinds of locks
**Between threads: mutexes**  
user mode (except when they block!)  
exploit shared memory  
binary: locked or unlocked  

**Between threads: unnamed semaphores**  
user mode (except when they block!)  
exploit shared memory  
they're integers  

**Between processes: named semaphores**  
kernel mode: live in kernel memory.  
no shared memory.  
They're integers.   

**Between machines: network semaphores**  
network objects  
use network communications  
no shared architecture.   

