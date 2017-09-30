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
```    

#### 关于 signal
信号分为实时信号和常规信号，前31个都是常规信号。已经产生但还没有传递的信号称为挂起信号(pendingsignal)。任何时候，一个进程仅存在给定类型的一个挂起信号，同一进程同种类型的其他信号不被排队，只被简单地丢弃。但是，实时信号是不同的：同种类型的挂起信号可以有好几个。

#### 关于 handler
handler 是一段代码，用来接收并处理指定信号。当进程接到指定信号时，会调用handler，处理之后返回interrupt的位置。但是 segfault 或者 bus error（SGEV or BUS）无法恢复(但是可以被接收并处理)。 