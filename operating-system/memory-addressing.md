# Addressing
_update 2017-11-15 13:22:26_

---
### View of memory:
![](/assets/Screen Shot 2017-11-15 at 1.37.26 PM.png)
Process 只能看到 logical pages，而 OS 则使用 physical memory maps；
(not finished)

### Cache
**A basic design principle:**  

* If a correspondence is large and sparse, use a hash.
* If a correspondence is large and dense, use a cache.
* If a correspondence is small, use an array.


