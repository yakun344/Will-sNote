# Threads

_update Oct 6,2017 15:42_

[linux进程间通信方式demo汇总](https://github.com/clpsz/linux-ipcs)： 这是一个不错的repo，值得一看。

## 关于 address space 和 task 以及 lightweight process

address space 大概相当于 process， task 大概相当于 thread，lightweight process 是 thread 的别称。

## 关于 process 和 thread

[这里](http://community.bittiger.io/topic/434/进程-process-和线程-thread-的区别/3) 有一个关于进程和线程区别的讨论。

总结一下，在多进程多线程模型中，processes are used to group resources together; Threads are the entities scheduled for execution on the CPU.

具体而言，process 包括了：

* an memory map \(包括 data\(global\)，stack，heap， text\)；
* a process control block （linux中的 task\_struct）\(包括pid，state，pc等很多内容\)；

而 thread 从属于 process。一个process下的所有 threads 共享了 process 的 memory map中的 （data\(global\)，heap, text），而同时每个 thread 拥有自己的 stack 和 thread control block（包括pc 和 register value）。

## 关于 TCB （Thread control block）

![](../.gitbook/assets/screen-shot-2017-10-08-at-11.12.25-pm.png)

TCB 包括了：

1. pointer to parent PCB;
2. PC/registers for thread;
3. stack location in memory map;

## 与 process 相比的优势

1. threads之间的通信方式更加灵活（more options than with pipes）。
2. 比 pipe 快，因为 pipe 存储在 kernel memory 中。
3. 创建 thread 比创建 process 快十倍。
4. terminate 也快。
5. 同一 process 的 threads 间切换也比 process 快。

## 关于 mutex\_lock

lock 和 unlock 都是在 user mode 完成的，只有当需要 block thread 的时候会进入 kernel mode。

## 应用

1. Hiding latency for multiple I/O requests; \(optimize run time\)

   > 1. Make multiple requests to web server, get answer asynchronously, one per thread.
   > 2. fork-join parallelism in web browser.
   > 3. a file system.

2. Producer / consumer program architecture; \(simplify coding\)

## 课上小程序示例分析

**简单创建一个thread，用pthread\_join回收，阻塞** 对于这段代码，起初对于 pthread\_join\(\) 所接收的二级指针参数 `(void **)&retptr` 有所疑问，但是仔细想来就明白了。因为我们需要让retptr等于thread\_routine的返回值，而其返回值是一个 `void * ret`，为了令`retptr == ret`，我们需要传入 `&retptr`，此时 `&retptr`的类型就变成了 `void**`。

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

**Threads race condition** 在routine中for loop 的前部分的 y=x 之后会sleep，此时切换执行其他thread，如此一来之后执行的x=y就会覆盖其他thread已经改过的x，导致最终x不会加到30；

```c
    int x=0;

    void *threaded_routine (void * v) {
        const int *n = (int *)v;
        int i;
        for (i=0; i<10; i++)  {
            int y=x;
            y++;
            printf("%d: y=%d\n",*n,y);
            sleep(1);
            x=y;
            printf("%d: x=%d\n",*n,x);
        }
    }

    main()
    {
       pthread_t thread1, thread2, thread3;
       void *retptr;
       int n1=1,n2=2,n3=3;

       pthread_create( &thread1, NULL, threaded_routine, (void *)&n1);
       pthread_create( &thread2, NULL, threaded_routine, (void *)&n2);
       pthread_create( &thread3, NULL, threaded_routine, (void *)&n3);

       printf("joining thread 1\n");
       pthread_join(thread1,(void **)&retptr);
       printf("joining thread 2\n");
       pthread_join(thread2,(void **)&retptr);
       printf("joining thread 3\n");
       pthread_join(thread3,(void **)&retptr);
       printf ("x = %d (should be 30)\n",x);
    }
```

**Using mutex\_lock**  mutex\_lock 的原理基于当pthread\_mutex\_lock\(\) 的参数已经被lock时，该函数会令当前thread wait直到locker可以被lock。

```c
    /* mutex locks to avoid race conditions */
    #include "./header.h"

    pthread_mutex_t locker;
    // pthread_mutex_init(&locker, NULL);
    // pthread_mutex_lock(&locker);
    // pthread_mutex_trylock(&locker);
    // pthread_mutex_unlock(&locker);
    // pthread_mutex_unlock(&locker);
    // pthread_mutex_destroy(&locker);

    int x=0;

    void *threaded_routine (void * v) {
        const int *n = (int *)v;
        int i;
        for (i=0; i<10; i++)  {
            pthread_mutex_lock(&locker);
            int y=x;
            y++;
            printf("%d: y=%d\n",*n,y);
            sleep(1);
            x=y;
            printf("%d: x=%d\n",*n,x);
            pthread_mutex_unlock(&locker);
        }
    }

    main()
    {
       pthread_t thread1, thread2, thread3;
       void *retptr;
       int n1=1,n2=2,n3=3;
       pthread_mutex_init(&locker, NULL);

       pthread_create( &thread1, NULL, threaded_routine, (void *)&n1);
       pthread_create( &thread2, NULL, threaded_routine, (void *)&n2);
       pthread_create( &thread3, NULL, threaded_routine, (void *)&n3);

       printf("joining thread 1\n");
       pthread_join(thread1,(void **)&retptr);
       printf("joining thread 2\n");
       pthread_join(thread2,(void **)&retptr);
       printf("joining thread 3\n");
       pthread_join(thread3,(void **)&retptr);
       printf ("x = %d (should be 30)\n",x);
       pthread_mutex_destroy(&locker);
    }
```

