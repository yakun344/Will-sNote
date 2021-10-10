# Memory Addressing

_update 2017-11-15 13:22:26_

## View of memory:

![](../../.gitbook/assets/screen-shot-2017-11-15-at-1.37.26-pm.png) Process 只能看到 logical pages，而 OS 则使用 physical memory maps； (not finished)

### Cache

**A basic design principle:**

* If a correspondence is large and sparse, use a hash.
* If a correspondence is large and dense, use a cache.
* If a correspondence is small, use an array.

**Influences upon memory addressing:**

1. Fragmentation: the fact that allocation patterns create holes in memory space.
2. Locality: the fact that most programs access memory with localized patterns of access; the likelihood that apage access will be followed by others is high.
3.  Security: the fact that processes should not be able to

    see other processes' space. 

### Page Tables (IA32)

A page table keeps track of the correspondence between pages and frames. Each logical address is separated into a page number and an offset;

Page table 也有可能不能完全加载，在出现page table cache miss 的时候，会发生如下：

1. interrupt the OS
2. load a new page table cache entry.
3. continue

### IA64 不用 page table，而是使用 translation lookaside buffer (TLB)

![](<../../.gitbook/assets/Screen Shot 2017-12-06 at 1.27.29 AM (1) (1).png>) ![](<../../.gitbook/assets/Screen Shot 2017-12-06 at 1.29.17 AM.png>) ![](<../../.gitbook/assets/Screen Shot 2017-12-06 at 1.29.32 AM.png>)

In practice, this is a huge (optimized) array access: `table[pgd index][pmd index][pte index]` ![](<../../.gitbook/assets/Screen Shot 2017-12-06 at 1.32.55 AM (1).png>)

### Virtual memory

![](<../../.gitbook/assets/Screen Shot 2017-12-06 at 1.35.12 AM.png>)

### Caches, hashes, and lookasides

![](<../../.gitbook/assets/Screen Shot 2017-12-06 at 1.39.51 AM.png>)
