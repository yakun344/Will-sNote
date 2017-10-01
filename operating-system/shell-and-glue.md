# Shell and Glue
_update Sep 30, 2017  21:25_

---
#### functions
```c
    int fd = fileno(fp);
    FILE *fp = fopen(fd);
```

#### 关于 open() 和 fopen() 以及 fdopen()
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

#### 关于 fd
fd 是与每个 process 独立关联，当 fork 的时候，child 会继承 parent 的 fd table。当执行 exec 时，fd 也会被继承，但是之前进程的memory和buffer都会被抛弃。