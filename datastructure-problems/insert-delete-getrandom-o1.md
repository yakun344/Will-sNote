## Insert Delete GetRandom O(1)
_update Aug 14,2017 20:46_

---
[LeetCode](https://leetcode.com/problems/insert-delete-getrandom-o1/description/)

Design a data structure that supports all following operations in average O(1) time.

insert(val): Inserts an item val to the set if not already present.
remove(val): Removes an item val from the set if present.
getRandom: Returns a random element from current set of elements. Each element must have the same probability of being returned.

**Example:**
```java
    // Init an empty set.
    RandomizedSet randomSet = new RandomizedSet();
    
    // Inserts 1 to the set. Returns true as 1 was inserted successfully.
    randomSet.insert(1);
    
    // Returns false as 2 does not exist in the set.
    randomSet.remove(2);
    
    // Inserts 2 to the set, returns true. Set now contains [1,2].
    randomSet.insert(2);
    
    // getRandom should return either 1 or 2 randomly.
    randomSet.getRandom();
    
    // Removes 1 from the set, returns true. Set now contains [2].
    randomSet.remove(1);
    
    // 2 was already in the set, so return false.
    randomSet.insert(2);
    
    // Since 2 is the only number in the set, getRandom always return 2.
    randomSet.getRandom();
```

#### Basic Idea:
这道题目是要求设计一个数据结构set，满足插入删除和getRandom，O(1) time for each operation.

提到O(1) 时间 getRandom，我们就想到使用数组（或者Java中的List），但是使用数组无法满足插入和删除O(1)时间，于是我们就想到把HashMap和List进行结合：

*  插入时候先在list中append，然后把index作为value，val作为key存入map；
*  删除的时候先从map中找到list中val的index，然后将其与最后一个元素换位，然后删除。并且更新刚刚换过来的元素在map中登记的index。

#### Java Code:
```java
    public class RandomizedSet {
    
        Map<Integer, Integer> map = null;
        List<Integer> lst = null;
        /** Initialize your data structure here. */
        public RandomizedSet() {
            map = new HashMap<>();
            lst = new ArrayList<>();
        }
        
        /** Inserts a value to the set. Returns true if the set did not already contain the specified element. */
        public boolean insert(int val) {
            if (map.containsKey(val)) return false;
            lst.add(val);
            map.put(val, lst.size() - 1);
            return true;
        }
        
        /** Removes a value from the set. Returns true if the set contained the specified element. */
        public boolean remove(int val) {
            if (! map.containsKey(val)) return false;
            // 先得到在lst中的index 
            int index = map.get(val);
            // 和list中最后一个元素换位, 删除最后一个
            if (index == lst.size() - 1) {
                lst.remove(index);
            } else {
                lst.set(index, lst.remove(lst.size() - 1));
                // 更新刚换到index的元素map中对应的index
                map.put(lst.get(index), index);
            }
            // remove map 中的 val
            map.remove(val);
            return true;
        }
        
        /** Get a random element from the set. */
        public int getRandom() {
            return lst.get((int)(lst.size() * Math.random()));
        }
    }
    
    /**
     * Your RandomizedSet object will be instantiated and called as such:
     * RandomizedSet obj = new RandomizedSet();
     * boolean param_1 = obj.insert(val);
     * boolean param_2 = obj.remove(val);
     * int param_3 = obj.getRandom();
     */
```



