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

```c
// sharing a queue between threads 
#include <stdio.h> 
#include <string.h> 
#include <pthread.h> 

// canonical solution to deadlock-free producer-consumer 
// coding, using three locks 
pthread_mutex_t can_modify; 	// whether I can modify queue contents 
pthread_mutex_t can_dequeue; 	// locked if queue is empty
pthread_mutex_t can_enqueue; 	// locked if queue is full 

#define SIZE 20
int begin=0, end=0; char queue[SIZE]; 		// extent of queue
int empty() { return begin==end; } 		// is queue empty? 
int full() { return ((end+1)%SIZE)==begin; } 	// is queue full? 

void enqueue(char c) {				// how to enqueue a character
    pthread_mutex_lock(&can_enqueue); 		// wait until there is space
    pthread_mutex_lock(&can_modify); 		// wait until you can modify
//////////////////BEGIN CRITICAL SECTION 
    int empty_before=empty(); 
    queue[end]=c; end=(end+1)%SIZE;
    int full_after=full(); 
//////////////////END CRITICAL SECTION 
    pthread_mutex_unlock(&can_modify); 
    if (empty_before) { 			// if it was empty before, 
	    pthread_mutex_unlock(&can_dequeue); 	// it is not empty now. 
    } 
    if (!full_after) { 				// if it is not full after, 
        pthread_mutex_unlock(&can_enqueue); 	// there is still space.
    } 
}

char dequeue() { 				// how to dequeue a character
    char out; 
    pthread_mutex_lock(&can_dequeue); 		// wait until you can dequeue
    pthread_mutex_lock(&can_modify); 		// wait until you can modify
//////////////////BEGIN CRITICAL SECTION 
    int full_before = full(); 
    out = queue[begin]; begin=(begin+1)%SIZE; 
    int empty_after = empty(); 
//////////////////END CRITICAL SECTION 
    pthread_mutex_unlock(&can_modify); 
    if (full_before) { 				// if it was full before,
    	pthread_mutex_unlock(&can_enqueue); 	// then it is not full now.
    } 
    if (!empty_after) { 			// if it was not empty after, 
    	pthread_mutex_unlock(&can_dequeue); 	// there is something to dequeue
    } 
    return out; 
}

```

















