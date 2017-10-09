## Threads
_update Oct 6,2017  15:42_

---
#### 关于 address space 和 task 以及 lightweight process
address space 大概相当于 process， task 大概相当于 thread，lightweight process 是 thread 的别称。

#### 关于 process 和 thread
[这里](http://community.bittiger.io/topic/434/%E8%BF%9B%E7%A8%8B-process-%E5%92%8C%E7%BA%BF%E7%A8%8B-thread-%E7%9A%84%E5%8C%BA%E5%88%AB/3) 有一个关于进程和线程区别的讨论。

总结一下，在多进程多线程模型中，processes are used to group resources together; Threads are the entities scheduled for execution on the CPU.

具体而言，process 包括了： 

*  an memory map (包括 data(global)，stack，heap， text)；
*  a process control block （linux中的 task_struct）(包括pid，state，pc等很多内容)；

而 thread 从属于 process。一个process下的所有 threads 共享了 process 的 memory map中的 （data(global)，heap, text），而同时每个 thread 拥有自己的 stack 和 thread control block（包括pc 和 register value）。