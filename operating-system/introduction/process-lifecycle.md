# Process Lifecycle

_update Sep 18, 2017 23:31_

This note is for the 3rd class of _Tufts comp111 2017 fall_.

这节课主要介绍了一些有关 process 的内容，课堂上老师提供了很多不错的示例程序，但是基本上，需要先对一些 system function 的用法和原理有了解。

### Mode Switching

在 interrupt stage, 处理器查看有无 pending interrupt（signal），如果没有，则进入 fetch stage 开始执行当前进程的下一条指令；如果有 pending interrupt，processor 做如下事情：

1. 将pc（program counter）设为 interrupt handler 的 starting address；
2. 从 user mode 进入 kernel mode，从而使得 interrupt processing 变得 privileged；

另外，开始执行 handler 时，已经将process的context存入了 process control block。（这里的context需要保存所有可能被 handler 执行所影响的内容，包括 **pc， processor registers， stack information**）;

因为 system call 会造成 mode switching，需要改变context，耗时，所以例如在进行写操作的时候，我们使用 buffered printf 而不是直接 write\(\)，这样可以减少 system call 的数量。

### Changing Process State

Mode switch 不一定需要更改process的状态，但是进行process state changing 的步骤比较复杂：

1. 保存 processor contex，包括 pc 和其他 regesters；
2. 更新 process control block 中当前 Running 状态的进程信息，包括更改 state，更新离开 Running sate 的原因等等；
3. 将 process control block 移动到合适的queue中（Ready, Blocked on Event i, Ready/Suspend）； 
4. 选择另一个process执行；
5. 更新选择的 process 的 control block，把 state 设为 Running；
6. 更新 memory management data structure;
7. 将 context of processor 恢复到刚选中的 process 离开 Running 时候的状态；

### 关于 SIGCHLD

Linux 中默认对于 SIGCHLD 是 ignore 的，我们可以通过自定义 signal handler 来实现异步 reap 子进程结束码的操作。但是需要注意，如果短时间内有大量 SIGCHLD 同时到达, 系统不会对其进行排队，而是直接丢弃，这也是为什么在 handler 中必须使用 while loop + WNOHANG option 进行 waitpid 的操作。如果不这么做，就有一定概率一些 SIGCHLD 会被丢弃，从而产生 zombie 进程。

但是，如果我们在 handler 中使用 while loop + waitpid\(WNOHANG\)，相当于每次接收到 SIGCHLD 的时候都会进入循环，直到 waitpid\(WNOHANG\) 返回 0 为止，此时可以确定当前没有遗留的 zombie。

另外要注意，wait 函数和 SIGCHLD 是没有关系的，SIGCHLD 只是用来触发 handler 调用 wait。即使之前有 SIGCHLD 因为拥挤被抛弃了，循环调用 wait 的时候，仍然可以处理掉。

### 关于 fork\(\)

fork\(\) 会将当前进程分裂为两个，并且child会继承parent几乎所有的属性，include pc and buffer, 这也是为什么 child 会在 fork\(\) 的位置继续执行，而且在 fork 之前 buffer 内没有 flush 的内容最终会被 print 两遍的原因。

如果 parent 先死，则其所有child（包括死后产生的zombie）都会被 pid 为 1 的 init 收养，最终被回收。

如果 child 先死，parent 需要通过 直接wait 或者调用 signal\(SIGCHLD, handler\) 的方法处理死去的孩子，否则的话孩子会变成 zombie. 如果parent持续产生子进程而又不处理，就会产生很多僵尸进程占满 process table，就会出现 resource leak。

因为 process switch 会需要时间，在不涉及 I/O 操作的时候，fork 很多子进程分别工作会比直接逐一操作更慢。但是在涉及到大量 I/O 访问的情况下，比如 web server，fork 就很有好处。

### 关于 exec\(\)

exec\(\) 会将当前进程的执行代码替换为exec要执行的代码，但是不改变pid，同时，之前设置的环境变量和fd之类的context会保持不变。

### 关于 Linux 环境变量

[这里](http://www.jianshu.com/p/ac2bc0ad3d74) 有一篇很好的文章。

## Small Programs in Class

### running a "cat" command in the foreground with explicit wait:

```c
    /* polling for child exit */
    #include <stdio.h>
    #include <unistd.h>
    #include <signal.h>
    #include <sys/types.h>
    #include <sys/time.h>
    #include <sys/resource.h>
    #include <sys/wait.h>

    main()
    {
        pid_t pid1, pid2; int status;
        struct rusage usage;

        if ((pid1=fork())) {    // fork 返回孩子的 pid 说明是 parent
            printf("I am parent %d; child is %d\n",getpid(),pid1);
        } else {            // 返回 0 说明是 child
            printf("child %d: waiting 5 seconds.\n", getpid());
            sleep(5);
            printf("child %d: calling cat...\n", getpid());
            execl("/bin/cat", "/bin/cat", "/comp/111/news/0001.txt", NULL);
            printf("we should never get here!\n");
        }

        printf("there's life after forking!\n");
        // 先执行的是parent，创建child之后并不会马上执行，child 会先进入 ready 状态
        // 直到进入下面的循环，打印一次 zzz 之后，parent 开始 sleep，此时再开始执行child
        while (!(pid2 = waitpid(pid1, &status, WNOHANG))) {
            // 上面的 waitpid 函数，option 参数是 WNOHANG 时，如果 status 没有改变，会返回 0
            // 改变后，会返回 pid
            printf("zzz...\n");
            sleep (1);
        }
        printf("exit code for %d is %d\n", pid2, status);
    }
```

output:

```text
    I am parent 26001; child is 26002
    there's life after forking!
    zzz...                               # 此时执行了第一次while，之后parent sleep
    child 26002: waiting 5 seconds.      # 执行 child，child sleep 5sec
    zzz...
    zzz...                           
    zzz...
    zzz...
    child 26002: calling cat...          # child 睡醒，继续执行
    zzz...                               # parent 睡醒，print之后继续sleep
    Welcome to COMP111. Please stay tuned here for important information.
                                         # 之后 child结束，waitpid 返回 >0，结束
```

### Example: running a "cat" command in the background with implicit wait:

```c
    /* reaping children through SIGCHLD */
    #include <stdio.h>
    #include <unistd.h>
    #include <signal.h>
    #include <sys/types.h>
    #include <sys/time.h>
    #include <sys/resource.h>
    #include <sys/wait.h>

    #define FALSE 0
    #define TRUE  1

    int done = FALSE;

    // 定义一个signal handler，相当于call back 被传入的function
    void reaper(int sig) {
        int status;
        pid_t pid2 = waitpid(-1, &status, 0);
        printf("...child of %d done calling cat.\n",getpid());
        printf("exit code for %d is %d\n", pid2, status);
        done=TRUE;  // I reaped it, so I can exit myself.
    }

    main() {
        pid_t pid1;

        // 当接收到signal：SIGCHLD时，会交由传入的handler（reaper）处理    
        signal(SIGCHLD,reaper);

        if ((pid1=fork())) {    // parent
            printf("I am parent %d; child is %d\n",getpid(),pid1);
        } else {            // child
            printf("child %d: waiting 5 seconds.\n",getpid());
            sleep(5);
            printf("child %d: calling cat...\n", getpid());
            execl("/bin/cat", "/bin/cat", "/comp/111/news/0001.txt", NULL);
            printf("we should never get here!\n");
        }
        printf("there's life after forking!\n");

        while (!done) {
            printf("doing something...\n"); sleep(1);
        }
        printf("got my SIGCHLD, cleaning up!\n");

        // SIG_DFL 表示 default，恢复 SIGCHLD的默认处理
        signal(SIGCHLD,SIG_DFL);
    }
```

output:

```text
I am parent 26359; child is 26360
there's life after forking!
doing something...
child 26360: waiting 5 seconds.
doing something...
doing something...
doing something...
doing something...
child 26360: calling cat...
doing something...
Welcome to COMP111. Please stay tuned here for important information.
...child of 26359 done calling cat.
exit code for 26360 is 0
got my SIGCHLD, cleaning up!
```

### running a user-typed command in the foreground without arguments:

这个程序可以类似于shell一样，新建child process运行用户的程序，parent则等待，之后重复过程。

```c
    #include <stdio.h>
    #include <unistd.h>
    #include <string.h>
    #include <sys/types.h>
    #include <sys/wait.h>

    #define SIZE 256

    int main(int argc, char **argv, char **envp)
    {
        char command[SIZE];
        int cmdlen; /* command length */
        int pid;    /* process identifier */
        int status; /* status code for child */

        while (1) {
            /* get a command */
            printf("by your command... ");
            fgets(command,SIZE,stdin);

            /* get rid of \n at end */
            cmdlen = strlen(command);
            if (cmdlen<256)
                command[cmdlen-1]='\0';

            /* now run it, as if we were a shell */
            if (pid=fork()) { /* parent */
                printf("child %d forked\n", pid);

                // 等待 child 进程结束，执行下一行printf语句之后进行下一个循环
                int newpid = waitpid(-1, &status, 0);
                printf("child %d exited with status %d\n", newpid, status);
            } else {    /* child */
                execl(command,command,NULL);

                // 如果执行成功，则不会到这里
                printf("command execution failed.\n");
            }
        }
    }
```

### running a user-typed command in the background without arguments:

这是主程序，用户可以输入一个可执行文件，然后会新建子进程执行，同时parent并不wait，当子程序结束的时候，SIGCHLD 会被 reaper 处理；

```c
    #define FALSE 0
    #define TRUE  1

    int done = FALSE;

    void reaper(int sig) {
        int status;
        pid_t pid2 = waitpid(-1, &status, 0);
        printf("exit code for child %d is %d\n", pid2, status);
        done=TRUE;
    }

    #define SIZE 256

    int main(int argc, char **argv, char **envp)
    {
        char command[SIZE];
        int cmdlen; /* command length */
        int pid;    /* process identifier */
        int status; /* status code for child */

        signal(SIGCHLD, reaper);

        while (1) {
            /* get a command */
            printf("by your command... ");
            fgets(command,SIZE,stdin);

            /* get rid of \n at end */
            cmdlen = strlen(command);
            if (cmdlen<256)
                command[cmdlen-1]='\0';

            /* now run it, as if we were a shell */
            if (!(pid=fork())) { /* child */
                execl(command,command,NULL);
                printf("command execution failed\n");
            }
        }
    }
```

这是用来测试的可执行文件的代码：

```c
    int main() {
        for (int i = 0; i < 5; ++i) {
            sleep(2);
        }
        printf("I'm %d, I'm gone\n", getpid());
        return 0;
    }
```

output:

```text
    vm-hw08{xguo04}75: ./a.out
    by your command... ./sleep.out
    by your command... ./sleep.out
    by your command... ./sleep.out
    by your command... ./sleep.out
    by your command... ./sleep.out
    by your command... ./sleep.out
    by your command... I'm 4599, I'm gone
    exit code for child 4599 is 0
    I'm 4600, I'm gone
    exit code for child 4600 is 0
    I'm 4601, I'm gone
    exit code for child 4601 is 0
    I'm 4602, I'm gone
    exit code for child 4602 is 0
    I'm 4603, I'm gone
    exit code for child 4603 is 0
    I'm 4604, I'm gone
    exit code for child 4604 is 0
^C
```

### Test for orphan process

```c
    /**************************************************
    *                                                 *
    * 测试，parent先退出之后，child会变为孤儿，被init收养   *
    *                                                 *
    **************************************************/

    #include "./header.h"

    int main() {
        int pid = fork();
        if (pid < 0) { // failed
            printf("fork failed\n");
        } else if (pid > 0) { // parent
            printf("Im the parent of %d\n", pid);
            sleep(1); // 睡一秒，保证子进程第一次输出当前parent的ID
            printf("Parent terminated\n");
        } else { // child
            printf("Im %d, my parent is %d\n", getpid(), getppid());
            sleep(5); // 保证parent已经退出
            printf("After sleeping, Im %d, my parent is %d\n", getpid(), getppid());
        }

        return 0;
    }
```

output

```text
    vm-hw03{xguo04}68: ./a.out
    Im the parent of 10874
    Im 10874, my parent is 10872
    Parent terminated
    vm-hw03{xguo04}69: After sleeping, Im 10874, my parent is 1
```

### Test for zombie process \(僵尸孩子\)

使用 `ps aux | grep 'Z'` 可以检查zombie process；

```c
    /*******************************************************
    *                                                      *
    * 测试，子程序先退出，parent不做 reap，child 会变成 zombie。 *
    *                                                      *
    ********************************************************/

    #include "./header.h"
    int main() {
        printf("create 10 child, make them be zombies\n");
        for (int i = 0; i < 10; ++i) {
            if (! fork()) {
                printf("Im a child, Im gonna to be a zombie\n");
                exit(0);
            }
        }    
        sleep(1); // ensure children exit first
        system("ps aux | grep 'Z'");

        return 0;
    }
```

output:

```text
    vm-hw04{xguo04}66: ./a.out
    create 10 child, make them be zombies
    Im a child, Im gonna to be a zombie
    Im a child, Im gonna to be a zombie
    Im a child, Im gonna to be a zombie
    Im a child, Im gonna to be a zombie
    Im a child, Im gonna to be a zombie
    Im a child, Im gonna to be a zombie
    Im a child, Im gonna to be a zombie
    Im a child, Im gonna to be a zombie
    Im a child, Im gonna to be a zombie
    Im a child, Im gonna to be a zombie
    USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
    xguo04   16010  0.0  0.0      0     0 pts/6    Z+   15:55   0:00 [a.out] <defunct>
    xguo04   16011  0.0  0.0      0     0 pts/6    Z+   15:55   0:00 [a.out] <defunct>
    xguo04   16012  0.0  0.0      0     0 pts/6    Z+   15:55   0:00 [a.out] <defunct>
    xguo04   16013  0.0  0.0      0     0 pts/6    Z+   15:55   0:00 [a.out] <defunct>
    xguo04   16014  0.0  0.0      0     0 pts/6    Z+   15:55   0:00 [a.out] <defunct>
    xguo04   16015  0.0  0.0      0     0 pts/6    Z+   15:55   0:00 [a.out] <defunct>
    xguo04   16016  0.0  0.0      0     0 pts/6    Z+   15:55   0:00 [a.out] <defunct>
    xguo04   16017  0.0  0.0      0     0 pts/6    Z+   15:55   0:00 [a.out] <defunct>
    xguo04   16018  0.0  0.0      0     0 pts/6    Z+   15:55   0:00 [a.out] <defunct>
    xguo04   16019  0.0  0.0      0     0 pts/6    Z+   15:55   0:00 [a.out] <defunct>
    xguo04   16020  0.0  0.0 113132  1208 pts/6    S+   15:55   0:00 sh -c ps aux | grep 'Z'
    xguo04   16022  0.0  0.0 112668   936 pts/6    S+   15:55   0:00 grep Z
```

### Test for buffer and fork

```c
    /*****************************************************
       如果不手动 flush stdout buffer，第一句会打印两次，因为子进
       程会继承未刷新的 buffer；

       当在fork之前手动 flush buffer 之后，第一句就只会打印一次了
    ******************************************************/
    #include "./header.h"
    int main(int argc, char const *argv[]) {
        printf("This is gonna be printed");
        // fflush(stdout);  // explicitly flush stdout buffer 
        if (fork()) {
            printf("...enventually\n");
        } else {
            printf("...twice\n");
        }    
    }
```

Output\(don't flush\)

```text
This is gonna be printed...enventually
This is gonna be printed...twice
```

flush:

```text
This is gonna be printed
...enventually
...twice
```

