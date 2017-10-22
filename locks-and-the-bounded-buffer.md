## Locks and the Bounded Buffer
_update Oct 21,2017  12:55_

#### 3 Operations
**Atomic Operation**: An atomic operation is something that is not interruptable and occurs in isolation from other operations.  
**thread-safe operation**: A thread-safe operation (or set of operations) has the property that if used by multiple threads, no problems will occur.  
**async-safe operation**: An async-safe operation (also called reentrant in some circles) is an operation that can safely be invoked in a signal handler. 

#### What is a pipe?
Semantically, a **ring buffer of characters** (an array
queue).  
**write** enqueues to the buffer.  
**read** dequeues from the buffer.  
**Thread-safety** and async safety.  
**simultaneous** usability by multiple processes.   
**block on** read from empty buffer.  
**block on** write to full buffer.  

#### Bounded buffer queue
This is a basic building block for all producer/consumer programs. 



















