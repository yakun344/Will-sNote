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

void *threaded_routine_1 (void * v) { 
    int i; 
    fprintf (stderr, "hello from the first thread!\n"); 
    for(i=0; i<200; i++) { 
        fprintf(stderr, "first  %03d: enqueue('%c')\n", i, 'a'+(i%26)); 
    	enqueue('a'+(i%26)); 
    } 
    fprintf (stderr, "bye from the first thread!\n"); 
    return NULL;
} 

void *threaded_routine_2 (void * v) { 
    int i; 
    fprintf (stderr, "hello from the second thread!\n"); 
    for(i=0; i<200; i++) { 
        fprintf(stderr, "second %03d: enqueue('%c')\n", i, 'A'+(i%26)); 
    	enqueue('A'+(i%26)); 
    } 
    fprintf (stderr, "bye from the second thread!\n"); 
    return NULL;
} 

void *threaded_routine_3 (void * v) { 
    int i; 
    fprintf (stderr, "hello from the third thread!\n"); 
    for(i=0; i<200; i++) { 
        fprintf(stderr, "third  %03d: enqueue('%c')\n", i, '0'+(i%10)); 
    	enqueue('0'+(i%10)); 
    } 
    fprintf (stderr, "bye from the third thread!\n"); 
    return NULL;
} 

main()
{ 
   pthread_t thread1,thread2,thread3; 
   void *retptr; 
   int i; 
   pthread_mutex_init(&can_modify, NULL);  // not being modified now => unlocked
   pthread_mutex_init(&can_enqueue, NULL); // not full now => unlocked 
   pthread_mutex_init(&can_dequeue, NULL);
   pthread_mutex_lock(&can_dequeue); 	   // empty now => LOCKED 

   fprintf(stderr,"hello from the parent... creating threads!\n"); 
   pthread_create( &thread1, NULL, threaded_routine_1, NULL); 
   pthread_create( &thread2, NULL, threaded_routine_2, NULL); 
   pthread_create( &thread3, NULL, threaded_routine_3, NULL); 
   for (i=0; i<600; i++) { 
    	char c = dequeue(); 
    	fprintf(stderr, "parent %03d: dequeue() is '%c'\n",i,c); 
   } 
   pthread_join(thread1,(void **)&retptr); 
   pthread_join(thread2,(void **)&retptr); 
   pthread_join(thread3,(void **)&retptr); 
   pthread_mutex_destroy(&can_modify);
   pthread_mutex_destroy(&can_enqueue);
   pthread_mutex_destroy(&can_dequeue);
   fprintf(stderr, "bye from the parent!\n"); 
} 
```

















