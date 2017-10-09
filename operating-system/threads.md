## Threads
_update Oct 6,2017  15:42_

---
[linux进程间通信方式demo汇总](https://github.com/clpsz/linux-ipcs)： 这是一个不错的repo，值得一看。

#### 关于 address space 和 task 以及 lightweight process
address space 大概相当于 process， task 大概相当于 thread，lightweight process 是 thread 的别称。

#### 关于 process 和 thread
[这里](http://community.bittiger.io/topic/434/%E8%BF%9B%E7%A8%8B-process-%E5%92%8C%E7%BA%BF%E7%A8%8B-thread-%E7%9A%84%E5%8C%BA%E5%88%AB/3) 有一个关于进程和线程区别的讨论。

总结一下，在多进程多线程模型中，processes are used to group resources together; Threads are the entities scheduled for execution on the CPU.

具体而言，process 包括了： 

*  an memory map (包括 data(global)，stack，heap， text)；
*  a process control block （linux中的 task_struct）(包括pid，state，pc等很多内容)；

而 thread 从属于 process。一个process下的所有 threads 共享了 process 的 memory map中的 （data(global)，heap, text），而同时每个 thread 拥有自己的 stack 和 thread control block（包括pc 和 register value）。

#### 关于 TCB （Thread control block）
![](/assets/Screen Shot 2017-10-08 at 11.12.25 PM.png)

TCB 包括了：

1.  pointer to parent PCB;
2.  PC/registers for thread;
3.  stack location in memory map;

#### 与 process 相比的优势
1.  threads之间的通信方式更加灵活（more options than with pipes）。
2.  比 pipe 快，因为 pipe 存储在 kernel memory 中。

#### 应用
1.  Hiding latency for multiple I/O requests; (optimize run time)
> 1. Make multiple requests to web server, get answer asynchronously, one per thread.
> 2. fork-join parallelism in web browser.

2.  Producer / consumer program architecture; (simplify coding)

#### 课上小程序示例分析
**简单创建一个thread**
对于这段代码，起初对于 pthread_join() 所接收的二级指针参数 `(void **)&retptr` 有所疑问，但是仔细想来就明白了。因为我们需要让retptr等于thread_routine的返回值，而其返回值是一个 `void * ret`，为了令`retptr == ret`，我们需要传入 `&retptr`，此时 `&retptr`的类型就变成了 `void**`。
```c
    #include "header.h"
    
    struct inputdata {
        char name[20];
    } ;
    
    struct outputdata {
        int idno;
    } x ;
    
    void *threaded_routine (void * v) {
        struct inputdata *d = (struct inputdata *)v;
        printf ("hello from the thread!\n");
        printf ("my name is %s\n",d->name);
        sleep(5);
        printf ("setting idno\n");
        x.idno = 42;
        printf ("bye from the thread!\n");
        return (void *) &x;
    }
    
    main() {
        pthread_t thread;
        printf("hello from the parent... creating thread\n");
        struct inputdata input;
        strcpy(input.name,"George");
        struct outputdata *retptr; 
        if (pthread_create( &thread, NULL, threaded_routine, (void *)&input)==0) {
            // 因为要改动 retptr 的值（令其等于routine返回的（void*）&x）, 所以必须使用二级指针
            pthread_join(thread,(void **)&retptr); 
            printf("got id number %d\n", retptr->idno);
        } else { 
            printf("could not create thread!\n");
        }
        printf("bye from the parent\n");
    }
```    

