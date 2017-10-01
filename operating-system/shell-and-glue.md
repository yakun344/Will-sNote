# Shell and Glue
_update Sep 30, 2017  21:25_

---
#### functions
```c
    int fd = fileno(fp);
    FILE *fp = fopen(fd);
```

#### 关于 fd
fd 是与每个 process 独立关联，当 fork 的时候，child 会继承 parent 的 fd table。当执行 exec 时，fd 也会被继承，但是之前进程的memory和buffer都会被抛弃。