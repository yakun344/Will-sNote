# Pointers and Types

_update 2017-10-01 02:47:10_

## 关于 malloc 和 free

malloc return a `(void *)` pointer to storage, data is uninitialized. 如果不 initialize，malloc 耗时 O(1)，否则的话就是 `O(#bytes)`。而 free(p) 的时候会释放 p 指向的内存，使其可以被 malloc 重新分配给其他指针，当 p 已经被释放后对 `*p` 进行操作是危险的，会影响到被重新分配的内存。

## 关于const

![](<../../.gitbook/assets/screen-shot-2017-10-01-at-2.27.14-pm (1).png>)

紧跟在 const 后的才是 constant 的。

一般而言在function 中 declare 为 const 的参数都是不会被 touch 的，而 non-const 的都会被 touch。

**String Constant** 存储在 text segment 中，想要写会出发 bus error。

## 关于 `void*` 指针

`void*`pointer has no length, 所以不可以进行 add nor access，需要先 cast。

## 关于复杂的 C types

[这里](http://blog.csdn.net/zhangnannan\_/article/details/40677569) 有一篇不错的讲解。

[这里](http://www.cnblogs.com/windlaughing/archive/2013/04/10/3012012.html) 有一个关于函数指针的讲解。

**fun() 和 (\*fun)()**

事实上两者在调用的时候是等价的，C 语言中函数名的本质其实是一个函数指针，反应了指令执行时候的跳转地址，为了方便才令 fun() 可以做到 `(*fun)()` 的工作。但是在 declare 的时候不大相同，通过 `void fun()` 声明的函数，fun 是一个函数指针常亮，不可以被赋值。而通过 `void (*fun)()` 声明的函数，fun 是一个函数指针变量，可以被赋值为其他有着同样 parameter and return type 的函数。

**注意**：如果一个 function 被 called with extra parameters， they will be ignored。但是如果参数不够，则会有奇怪的 stack或register中的东西被识别为参数，会出错。

## 关于 static 和 extern

[这里](http://www.swanlinux.net/2013/05/16/c_static/) 有一个关于static的讲解；

如果可以的话尽量把变量定义为 static，limited scope；

extern 告诉编译器某变量在其他地方定义了；

## 关于 typedef

**eg: **`typedef int (*fint)()`

typedef 右边的部分相当于一个变量的声明，此处声明了一个叫做 fint 的变量，类型则是一个 返回int，没有参数的函数指针。加上了 typedef 之后，相当于这个 fint 本身变成了一种类型，可以用来定义该类型的其他的变量，而类型就是typedef 右边部分定义的类型。

## 应用

考虑到 portability，比如不同系统对于int的长度定义不同，所以在操作系统中一般看不到int，只有 int32\_t, int64\_t 之类的。

同样的，很多 data 被定义为 `void*` 就是因为不同系统对该 data 的定义不同；

**创建一个线程**

```c
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

    main()
    { 
       pthread_t thread; 
       printf("hello from the parent... creating thread\n"); 
       struct inputdata input; 
       strcpy(input.name,"George"); 
       struct outputdata *retptr; 
       if (pthread_create( &thread, NULL, threaded_routine, (void *)&input)==0) { 
        pthread_join(thread,(void **)&retptr); 
            printf("got id number %d\n", retptr->idno); 
       } else { 
        printf("could not create thread!\n"); 
       } 
       printf("bye from the parent\n"); 

    }
```
