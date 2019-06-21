# Atomicity and semaphores

_update Oct 22, 2017 15:19_

## Kinds of atomicity

**synthetic \(voluntary\) atomicity**: programs and/or threads obey locking to avoid operating at the same time.  
**kernel atomicity**: when a system call is executing, the scheduler won't interrupt it.  
**instruction atomicity**: some machine instructions preclude concurrent operations.  
**special-purpose system calls**: some specific system calls execute program instructions in an atomic \(uninterruptible\) fashion.

## Kinds of locks

**Between threads: mutexes**  
user mode \(except when they block!\)  
exploit shared memory  
binary: locked or unlocked

**Between threads: unnamed semaphores**  
user mode \(except when they block!\)  
exploit shared memory  
they're integers  
Unlimited numbers available.

**Between processes: named semaphores**  
kernel mode: live in kernel memory.  
no shared memory.  
They're integers. An array offset in a kernel table.  
Limited number available.

**Between machines: network semaphores**  
network objects  
use network communications  
no shared architecture.

## What is a mutex

Basically, one word of **user** memory.  
Utilize a simple race condition to acquire lock, based upon **atomicity of operations in assembly language**. If the word of memory is &gt; 0, then a lock has been acquired.  
If the word of memory == 0, then there is no lock.  
Testset: **if word==0 then word=1** as an **atomic operation**.

## what is a semaphore

A semaphore is an integer to which access is atomic.  
Initialized to some integer value.  
locks decrement it.  
unlocks increment it.  
value determines number of concurrent locks available.

## Mutex versus semphores

![](../.gitbook/assets/screen-shot-2017-10-22-at-4.15.51-pm%20%281%29.png)

* Mutexes can only be locked or unlocked, they are sometimes called "binary semaphores".
* Semaphores have an integer value that is the number of locks available. Can read a semaphore's value via sem\_getvalue\(&sem,&val\) where val is an int. 

## Using a semphore

![](../.gitbook/assets/screen-shot-2017-10-22-at-4.38.38-pm.png)

