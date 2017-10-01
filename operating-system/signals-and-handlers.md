# Signal and Handlers
_update Sep22, 2017  16:44_

---
[这里](http://wangyuxxx.iteye.com/blog/1703252) 有一个不错的关于 signal 的分析。
#### 基本命令和函数
```c
bash：
    killall -signal program_name: 用来发送指定信号给所有运行指定名字程序的 process 
    kill -signal pid: 发送指定信号给指定 pid 的 process
c:
    kill(pid, signal) : send signal to process with pid;
    kill(-pid, signal): 发送信号给 every processes in pid's group;
    kill(0, signal)   : 发送信号给当前进程所在 group 的所有进程;
        
        (A process group is a set of processes engaged in one task.
        By default, the children of a process are in the same group.
        One can put a process into a new group by calling setpgid().
        One's group id is the pid of the process that is group leader.
        One can query the group id via getpgid())
        
    signal(sig, handler): 用来调用接收处理指定信号的handler；    
    handler(sig): 处理指定sig信号；
    sigsetmask(sigmask(sig)): block 指定信号；
    sigsetmask(0): 移除 block；
    
```    
需要注意的是，如上的 signal(sig, handler) 函数已经 deprecated，我们应该用 POSIX 标准的 sigaction() 函数。还有关于sigmask的 posix 函数，详见下面的例子。需要注意，POSIX的sigaction可以避免例如 SIGUSR 在处理过程中多次到达积累，造成多次操作的问题。

#### 关于 signal
信号分为实时信号和常规信号，前31个都是常规信号。已经产生但还没有传递的信号称为挂起信号(pendingsignal)。任何时候，一个进程仅存在给定类型的一个挂起信号，同一进程同种类型的其他信号不被排队，只被简单地丢弃。但是，实时信号是不同的：同种类型的挂起信号可以有好几个。

#### 关于 handler
handler 是一段代码，用来接收并处理指定信号。当进程接到指定信号时，会调用handler，处理之后返回interrupt的位置。但是 segfault 或者 bus error（SGEV or BUS）无法恢复(但是可以被接收并处理)。SIGKILL (9) 无法被处理，会直直接终止进程。

### 课上所给程序
接收 SIGALARM，唤醒睡眠
```c
    #include "header.h"
    typedef enum { false, true } bool;
    
    int sleeping = false;
    
    void timeisup(int sig) {
      fprintf(stderr, "I hate it when the alarm wakes me!\n");
      sleeping = false;
    }
    
    main() {
       signal(SIGALRM,timeisup);
       alarm(3);     /* Please wake me in 3 seconds */ // 默认操作是终止当前进程
    
       sleeping = true;
       while (sleeping) {
         printf("zzz...\n");
         sleep(1);
       }
    
       fprintf(stderr, "Nuts, you woke me up!\n");
    }
```
Output:
```
couchvm01{xguo04}63: ./a.out
zzz...
zzz...
zzz...
I hate it when the alarm wakes me!
Nuts, you woke me up!
```

推迟 SIGINT
```c
      void handler(int sig) {
         printf("I received signal %d\n", sig);
         exit(1);
      }
      
      main() {
         int i;
         signal(SIGINT, handler);
         sigsetmask(sigmask(SIGINT)); // defer control-C until after the loop
         for (i=0; i<5; i++) { printf("zzz...\n"); sleep(1); }
         sigsetmask(0);
         printf("I completed normally\n");
      } 
```
output:
```
couchvm01{xguo04}82: ./a.out
zzz...
zzz...
^Czzz... // type ctrl-c here
zzz...
zzz...
I received signal 2
```

使用 POSIX 函数推迟 SIGINT （ctrl-c）
```c
    main() { 
        sigset_t mask; // 希望改成的mask
        sigset_t old_mask; // 之前的mask
        int i;
        sigemptyset(&mask); // initialize
        sigaddset(&mask, SIGINT); // 写 mask
        int ret = sigprocmask(SIG_BLOCK, &mask, &old_mask); // 应用mask的同时，将原mask保存于 old_mask中
        
        // now SIGINT is blocked (ignore control-C)
        // now do something without interruption
        fprintf(stderr, "now I betcha you can't control-C me!\n");
        for (i=0; i<10; i++) {
            fprintf(stderr, "zzz...\n");
            sleep(1);
        }
        ret = sigprocmask(SIG_UNBLOCK, &mask, &old_mask); // 用old_mask恢复当前使用的mask
    
        // now control-C works again.
        fprintf(stderr, "OK, now you can control-C me to stop me!\n");
        while (1) {
            fprintf(stderr, "zzz...\n");
            sleep(1);
        }
    }
```
output:
```




















