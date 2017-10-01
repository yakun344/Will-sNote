# Shell and Glue
_update Sep 30, 2017  21:25_

---
#### commands and functions
```c
    a.out > file : put stdout into a file;
    a.out >& file: put stdout and stderr into a file;
    a.out < file : take stdin from a file;
    p1 | p2      : put stdout of p1 into the stdin of p2;
    p1 |& p2     : put stdout and stderr of p1 into the stdin of p2;

    int fd = fileno(fp);
    FILE *fp = fdopen(fd); // buffers a raw descriptor
    int pipe(int[] pipefd[2]); // 生成一个pipe，[0] 是read end, [1] 是write end, 相当于
                               // 将生成的管道两端存入 pipefd[]; 
    dup(oldfd, newfd); // 将 oldfd 复制到 newfd，使得 newfd 指向同一个文件
    
```

#### 关于 open() 和 fopen() 以及 fdopen(）
```c
    FILE *fdopen(int fd, const char *mode);
    FILE *fopen(const char *path, const char *mode);
    int open(const char *pathname, int flags, mode_t mode);
```

`open()` is a low-level os call. `fdopen()` converts an os-level file descriptor to the higher-level FILE-abstraction of the C language. `fopen()` calls `open()` in the background and gives you a FILE-pointer directly.

1) fopen is a library function while open is a system call.
2) fopen provides buffered IO which is faster compare to open which is non buffered.
3) fopen is portable while open not portable (open is environment specific).
4) fopen returns a pointer to a FILE structure(FILE *); open returns an integer that identifies the file.
5) A FILE * gives you the ability to use fscanf and other stdio functions.

Line-buffered: 每次 “\n” 写入；
block-buffered: 每 8192 bytes 写入；
unbuffered：直接写入；

#### 关于 fd 和 FILE*
fd 是与每个 process 独立关联，当 fork 的时候，child 会继承 parent 的 fd table。当执行 exec 时，fd 也会被继承，但是之前进程的memory和buffer都会被抛弃。

fd 是 system call 的参数，而 FILE* 是buffered I/O 的参数，本质上是一个 file buffer 的指针。

|fd   | FILE*  | 
| --- | ------ |
|0    | stdin  |
|1    | stdout |
|2    | stderr |

#### profess 间通信的方法
1.  fork() parent can read statistics of child；
2.  one process can send signal to another: kill(pid, sig);
3.  a process can respond to a signal: signal(sig, handler);
4.  pipeline programming, typically: shell programming;
![](/assets/Screen Shot 2017-09-30 at 11.02.11 PM.png) ;

#### pipe
[这里](https://segmentfault.com/a/1190000009528245) 有一个不错的介绍，比较简洁，**一定要看**。

**以下是摘要：**  

1.  pipe 是一种只存在于内存中的FIFO文件, named pipe 则在磁盘中有实际文件；
2.  pipe 就像一个纸条，一边写入，一边读取，环形数组实现。当空的时候，read的process会wait，当满的时候，write的process会wait；
3.  创建过程：文中的图：  

![](https://sfault-image.b0.upaiyun.com/169/623/1696232898-59241c2eb4c67);














