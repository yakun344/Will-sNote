# Insert Delete GetRandom O(1) - Duplicates allowed (hard)
_update Jan 26,2018  10:57_

---
[LeetCode](https://leetcode.com/problems/insert-delete-getrandom-o1-duplicates-allowed/description/)

Design a data structure that supports all following operations in average O(1) time.

**Note:** Duplicate elements are allowed.

1. insert(val): Inserts an item val to the collection.
2. remove(val): Removes an item val from the collection if present.
3. getRandom: Returns a random element from current collection of elements. The probability of each element being returned is linearly related to the number of same value the collection contains.

**Example:**

    // Init an empty collection.
    RandomizedCollection collection = new RandomizedCollection();
    
    // Inserts 1 to the collection. Returns true as the collection did not contain 1.
    collection.insert(1);
    
    // Inserts another 1 to the collection. Returns false as the collection contained 1. Collection now contains [1,1].
    collection.insert(1);
    
    // Inserts 2 to the collection, returns true. Collection now contains [1,1,2].
    collection.insert(2);
    
    // getRandom should return 1 with the probability 2/3, and returns 2 with the probability 1/3.
    collection.getRandom();
    
    // Removes 1 from the collection, returns true. Collection now contains [1,2].
    collection.remove(1);
    
    // getRandom should return 1 and 2 both equally likely.
    collection.getRandom();
    
<br>

## Basic Idea:
和之前不允许重复的版本不同，这里需要考虑更多的细节：

 1.  首先，对于map中存储的index，这次需要用一个set来存储一个元素所有的index，之所以用set而不是list，是为了在remove的时候更方便地删除index；
 2.  在remove的时候，如果当前list的最后一个元素和要删除的元素相同，则选择删除最后一个元素，否则在换位的时候，更新set中index的时候会出问题；
 
* #### Java Code：
```java
class RandomizedCollection {
    private Map<Integer, Set<Integer>> map = new HashMap<>();
    private List<Integer> list = new ArrayList<>();
    /** Initialize your data structure here. */
    public RandomizedCollection() {}
    
    /** Inserts a value to the collection. Returns true if the collection did not already contain the specified element. */
    public boolean insert(int val) {
        Set<Integer> indexSet = map.get(val);
        if (indexSet != null) {
            // already contains 
            list.add(val);
            indexSet.add(list.size() - 1);
            return false;
        } else {
            // not already contain
            indexSet = new HashSet<>();
            list.add(val);
            indexSet.add(list.size() - 1);
            map.put(val, indexSet);
            return true;
        }
    }
    
    /** Removes a value from the collection. Returns true if the collection contained the specified element. */
    public boolean remove(int val) {
        Set<Integer> indexSet = map.get(val);
        if (indexSet == null) return false;
        // 拿出一个indexset中的index，用最后一个元素替换，然后删除最后一个元素, 如果
        // 删除之后 val 对应的元素全被被删掉，要将map中对应记录删除
        int index = indexSet.iterator().next();
        // 如果最后一个元素是val，选择删除最后一个元素
        if (list.get(list.size() - 1) == val) {
            index = list.size() - 1;
        }
        if (index == list.size() - 1) {
            // 直接删除最后一个
            list.remove(list.size() - 1);
        } else {
            // 用最后一个元素替换之后，删除最后一个元素
            int theLast = list.get(list.size() - 1);
            Set<Integer> indexSetOfTheLast = map.get(theLast);
            list.set(index, theLast); // 替换list中index位置为最后一个元素
            indexSetOfTheLast.remove(list.size() - 1); // 更新换过去元素的index
            indexSetOfTheLast.add(index);
            list.remove(list.size() - 1); // 删掉最后一个元素
        }
        // 更新 val 对应的 index，如果全部删掉，则删去其在map中的记录
        indexSet.remove(index);
        if (indexSet.isEmpty()) map.remove(val);
        return true;
    }
    
    /** Get a random element from the collection. */
    public int getRandom() {
        int index = (int)(Math.random() * list.size());
        return list.get(index);
    }
}
```
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    