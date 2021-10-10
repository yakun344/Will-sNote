# Shell and Glue

_update Sep 30, 2017 21:25_

为什么说dup是完全违背 functional programming 的？ 因为有 side effects。

## commands and functions

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

## 关于 open() 和 fopen() 以及 fdopen(）

```c
    FILE *fdopen(int fd, const char *mode);
    FILE *fopen(const char *path, const char *mode);
    int open(const char *pathname, int flags, mode_t mode);
```

`open()` is a low-level os call. `fdopen()` converts an os-level file descriptor to the higher-level FILE-abstraction of the C language. `fopen()` calls `open()` in the background and gives you a FILE-pointer directly.

1\) fopen is a library function while open is a system call. 2) fopen provides buffered IO which is faster compare to open which is non buffered. 3) fopen is portable while open not portable (open is environment specific). 4) fopen returns a pointer to a FILE structure(FILE _); open returns an integer that identifies the file. 5) A FILE _ gives you the ability to use fscanf and other stdio functions.

Line-buffered: 每次 “\n” 写入； block-buffered: 每 8192 bytes 写入； unbuffered：直接写入；

## 关于 fd 和 FILE\*

fd 是与每个 process 独立关联，当 fork 的时候，child 会继承 parent 的 fd table。当执行 exec 时，fd 也会被继承，但是之前进程的memory和buffer都会被抛弃。

fd 是 system call 的参数，而 FILE\* 是buffered I/O 的参数，本质上是一个 file buffer 的指针。

| fd | FILE\* |
| -- | ------ |
| 0  | stdin  |
| 1  | stdout |
| 2  | stderr |

## process 间通信的方法

1. fork() parent can read statistics of child；
2. one process can send signal to another: kill(pid, sig);
3. a process can respond to a signal: signal(sig, handler);
4.  pipeline programming, typically: shell programming;

    ![](<../../.gitbook/assets/screen-shot-2017-09-30-at-11.02.11-pm (1).png>) ;

## pipe

[这里](https://segmentfault.com/a/1190000009528245) 有一个不错的介绍，比较简洁，**一定要看**。 [这里](http://blog.csdn.net/shanshanpt/article/details/39049579) 还有一个dup() 的介绍；

**以下是摘要：**

1. pipe 是一种只存在于内存中的FIFO文件, named pipe 则在磁盘中有实际文件；
2. pipe 就像一个纸条，一边写入，一边读取，环形数组实现。当空的时候，read的process会wait，当满的时候，write的process会wait；
3. 创建过程：文中的图：  

![](https://sfault-image.b0.upaiyun.com/169/623/1696232898-59241c2eb4c67);

## 课上的小程序

**单程序读写pipe**

```c
    #define READER 0
    #define WRITER 1
    main()
    {
        int fd[2]; char buf[1024];
        if (pipe(fd)==0) {
            FILE *read = fdopen(fd[READER],"r");
            FILE *write= fdopen(fd[WRITER],"w");
            fprintf(write,"hi there\n"); // 在pipe的buffer中写入
            printf("I wrote: %s","hi there\n");
            fflush(write); // 正式写入pipe
            fgets(buf,1024,read); // 读 pipe，写入 buf 数组
            printf("I read: %s\n",buf); // 输出数组内容
        }
    }
```

output:

```
couchvm01{xguo04}60: ./a.out
I wrote: hi there
I read: hi there
```

**简单fork后，parent写入pipe，child读**

```c
    main()
    {
        pid_t pid1, pid2;
        int status;
        int fd[2];
        char buf[1024];
        struct rusage usage;
        pipe(fd); // 建立pipe
        if ((pid1=fork())) { // parent
            FILE *write = fdopen(fd[1],"w"); close(fd[0]); // parent 关读开写
            fprintf(write, "hi there!\n");
            fflush(write); fclose(write); // 写入pipe，之前child一直在等
            pid2=wait3(&status, 0, &usage);
            printf("exit code for %d is %d\n", pid2, status);
        } else {
            FILE *read = fdopen(fd[0],"r"); close(fd[1]); // child 关写开读
            fgets(buf,1024,read); fclose(read);
            printf("child %d got: '%s'\n", getpid(),buf);
        }
    }
```

output:

```
couchvm01{xguo04}63: ./a.out
child 12617 got: 'hi there!
'
exit code for 12617 is 0
```

**用pipe和dup实现 parent | cat，child 输出parent写入pipe的内容：**

```c
    main()
    { 
        pid_t pid1, pid2; 
        int status; 
        int fd[2]; 
        char buf[1024]; 
        struct rusage usage; 
        pipe(fd); 
        printf("fd[0]=%d, fd[1]=%d\n", fd[0], fd[1]); 
        if ((pid1=fork())) { // parent
            FILE *write = fdopen(fd[1],"w"); close(fd[0]); // parent 关读
            fprintf(write, "hi there!\n"); 
            fflush(write); fclose(write); 
            pid2=wait3(&status, 0, &usage); 
            printf("exit code for %d is %d\n", pid2, status); 
        } else { // child
            close(0); /* close existing stdin, so that 0 is unused */ 
            dup(fd[0]); /* copy fd[0] into slot 0 */ 
            close(fd[0]); /* close copy */ // read end已经dup，关掉原来的
            close(fd[1]); /* close other side */ // child 关写

            // 执行cat，此时cat的stdin已经是pipe的读端
            execl("/bin/cat", "cat", NULL); /* fool cat into echoing our input */ 
        } 
    }
```

output:

```
couchvm01{xguo04}67: ./a.out
fd[0]=3, fd[1]=4
hi there!
exit code for 15089 is 0
```

**实现 ps -ef (child | parent, 此处的parent相当于是shell)**

```c
    main()
    {
        pid_t pid1, pid2;
        int status;
        int fd[2];
        char buf[1024];
        struct rusage usage;
        pipe(fd);
        if ((pid1=fork())) { // parent
            FILE *read = fdopen(fd[0],"r"); close(fd[1]); // parent 关写
            if (read==NULL) {
                perror("cannot dup");
                exit(1);
            }
            while (!feof(read)) {
                char buf[256];
                fgets(buf,256,read);
                printf("parent: %s",buf);
            }
            pid2=wait3(&status, 0, &usage);
            printf("exit code for %d is %d\n", pid2, status);
        } else { // child
            close(1); /* close existing stdout, so that 1 is unused */
            dup(fd[1]); /* copy fd[1] into slot 1 */
            close(fd[1]); /* close copy */
            close(fd[0]); /* close other side */
            execl("/bin/ps", "ps", "-ef", NULL); /* call ps command */
        }
    }
```

**实现 ps -ef |& ./proc2** 基本思路就是将主程序作为parent，其stdin和执行ps的child的stdout相连，其stdout和执行proc2的child的stdin相连。

```c
    #define SIZE 256
    #define READ 0
    #define WRITE 1
    main()
    { 
        pid_t pid1, pid2, pid3; int status;
        int fd[2]; struct rusage usage;
        pipe(fd); // create the pipe first 
        if (!(pid1=fork())) {        // child 1 is the reader: "| proc2"
            close(fd[WRITE]);        // close unused side
            close(fileno(stdin)); 
            dup(fd[READ]); 
            close(fd[READ]);         // don't leave the unduped descriptor 
            execl(proc2, proc2, NULL) 
        } else if (!(pid2=fork())) { // child 2 is the writer: "proc1 |"
            close(fd[READ]);         // closed unused side
            close(fileno(stdout)); 
            dup(fd[WRITE]); 
            close(fd[WRITE]);        // don't leave the unduped descriptor
            execl(proc1, proc1, NULL); 
        } else {  // parent waits (or reaps) twice!
            pid3=wait3(&status, 0, &usage);       
            pid3=wait3(&status, 0, &usage);       
        } 
    }
```
