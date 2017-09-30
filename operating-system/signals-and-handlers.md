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

#### 关于 Handler
