# Pointers
_update 2017-10-01 02:47:10_

---
#### 关于 malloc 和 free
malloc return a (void *) pointer to storage, data is uninitialized. 如果不 initialize，malloc 耗时 O(1)，否则的话就是 O(#bytes)。