# Next Greater Element II

_update Jul 12, 2017 15:12_

[Leetcode](https://leetcode.com/problems/next-greater-element-ii/#/solutions)

Given a circular array \(the next element of the last element is the first element of the array\), print the Next Greater Number for every element. The Next Greater Number of a number x is the first greater number to its traversing-order next in the array, which means you could search circularly to find its next greater number. If it doesn't exist, output -1 for this number.

```text
Example 1:
Input: [1,2,1]
Output: [2,-1,2]
Explanation: The first 1's next greater number is 2; 
The number 2 can't find next greater number; 
The second 1's next greater number needs to search circularly, which is also 2.
```

**Note:** The length of given array won't exceed 10000.

### Basic Idea:

利用 `stack` 来加速，实现`O(n)`的复杂度。先把第一个元素的下标加入`stack`，从左往右查看，如果遇到比当前`peek`所对应元素大的元素，则当前`peek`所对应位置的`next greater element`为当前遇到的元素，`pop`之后再和新的`peek`比较。直到当前`element < nums[stack.peek()]`，把当前`element`的下标`push`。

### Java Code:

```java
// 对于每个 i，检查当前数字能否更新当前stack中数字的 next greater element，然后再把
//  i 本身 push
class Solution {
    public int[] nextGreaterElements(int[] nums) {
        Deque<Integer> stack = new ArrayDeque<>();
        int[] ret = new int[nums.length];
        Arrays.fill(ret, -1);
        for (int i = 0; i < 2 * nums.length; ++i) {
            while (! stack.isEmpty() && nums[stack.peekFirst()] < nums[i % nums.length]) {
                ret[stack.pollFirst()] = nums[i % nums.length];
            }
            if (i < nums.length) stack.offerFirst(i);
        }
        return ret;
    }
}
```

_update Jan 19,2018 11:07_

## Update: Next Greater Element I

这道题目和 II 相比，少了可以 search circularly，多了需要第二步用 HashMap 进行查找。 解法就是先对 nums2 求 NextGreaterElement，结果存入 HashMap, 然后对于每个 nums1 中元素，在map中查找其对应的 nextGreaterElement。

**Java Code**:

```java
class Solution {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        Map<Integer, Integer> map = new HashMap<>();
        Deque<Integer> stack = new ArrayDeque<>();
        for (int i = 0; i < nums2.length; ++i) {
            while (! stack.isEmpty() && nums2[stack.peekFirst()] < nums2[i]) {
                map.put(nums2[stack.pollFirst()], nums2[i]);
            }
            stack.offerFirst(i);
        }
        int[] ret = new int[nums1.length];
        for (int i = 0; i < nums1.length; ++i) {
            Integer nge = map.get(nums1[i]);
            ret[i] = nge == null ? -1 : nge;
        }
        return ret;
    }
}
```

**C++ Code:**

```cpp
class Solution {
public:
    vector<int> nextGreaterElement(vector<int>& findNums, vector<int>& nums) {
        deque<int> _stack;
        unordered_map<int, int> _map;
        for (int num : nums) {
            while (! _stack.empty() && _stack.back() < num) {
                _map.insert(pair<int, int>{_stack.back(), num});
                _stack.pop_back();
            }
            _stack.push_back(num);
        }

        vector<int> res;
        for (int num : findNums) {
            if (! _map.count(num)) {
                res.push_back(-1);
            } else {
                res.push_back(_map.find(num)->second);
            }
        }
        return res;
    }
};
```

