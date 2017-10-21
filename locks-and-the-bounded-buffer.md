## Locks and the Bounded Buffer
_update Oct 21,2017  12:55_

#### What is a pipe?
Semantically, a **ring buffer of characters** (an array
queue).
**write** enqueues to the buffer.
**read** dequeues from the buffer.