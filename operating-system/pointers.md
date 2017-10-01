# Pointers
_update 2017-10-01 02:47:10_

---
#### 关于 malloc 和 free
malloc return a `(void *)` pointer to storage, data is uninitialized. 如果不 initialize，malloc 耗时 O(1)，否则的话就是 `O(#bytes)`。而 free(p) 的时候会释放 p 指向的内存，使其可以被 malloc 重新分配给其他指针，当 p 已经被释放后对 `*p` 进行操作是危险的，会影响到被重新分配的内存。

#### 关于const
![](/assets/Screen Shot 2017-10-01 at 2.27.14 PM.png)

紧跟在 const 后的才是 constant 的。  
一般而言在function 中 declare 为 const 的参数都是不会被 touch 的，而 non-const 的都会被 touch。    

