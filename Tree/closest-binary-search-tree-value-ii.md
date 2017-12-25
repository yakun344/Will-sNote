## Closest Binary Search Tree Value II
_update Sep 1, 2017  0:09_

---
[LeetCode](https://leetcode.com/problems/closest-binary-search-tree-value-ii/description/)

Given a non-empty binary search tree and a target value, find k values in the BST that are closest to the target.

**Note:**
Given target value is a floating point.
You may assume k is always valid, that is: k ≤ total nodes.
You are guaranteed to have only one unique set of k values in the BST that are closest to the target.

**Follow up:**
Assume that the BST is balanced, could you solve it in less than O(n) runtime (where n = total nodes)?

#### Basic Idea:
**基本思路：**
最简单的思路当然是inorder遍历一遍 BST，维持一个size为 k 的最大堆，key 是 `abs(val-target)` ，沿途更新（每次淘汰堆顶距离target最大的val）。但是这样的做法当然配不上这样一道 Hard 难度的题目。要想体会这道题目的深深恶意，必须考虑低于 O(n) 的解法；

这里附上随手写的这种做法的 Python code:
```python
    class Solution(object):
        def closestKValues(self, root, target, k):
            """
            :type root: TreeNode
            :type target: float
            :type k: int
            :rtype: List[int]
            """
            def inorder(root):
                if not root: return
                inorder(root.left)
                heapq.heappush(pq, (-abs(root.val - target), root.val))
                if len(pq) > k:
                    heapq.heappop(pq)
                inorder(root.right)
            
            pq = []
            inorder(root)
            return [t[1] for t in pq]
```

**更快的方法：**
我们注意到事实上这道题目很像一道`二分法 + 2 pointers`的题 [K Closest Numbers In Sorted Array](https://will-gxz.gitbooks.io/xiaozheng_algo/content/binary-search/k-closest-numbers-in-sorted-array.html), 在这道题目中，我们先用二分法找到最近的位置，然后用 2 pointers 向两边扩展，耗时 O(logN + k)。

那么在现在这道题中，无疑非常相似，不同之处在于使用双指针没有那么方便，因为我们需要持续地获取 predecessor 和 successor，而题目中的 TreeNode 没有 parent 指针。

于是，一种思路浮现在脑海，我们可以写两个函数，分别获取某节点的 predecessor 和 successor，每次耗时最坏情况下为 O(logN), 而我们共需要找 k 次，总计耗时 O(klogN)，一般来说这是优于 O(n) 的，所以我最先写了如下代码：

Java Code:
```java
    class Solution {
        public List<Integer> closestKValues(TreeNode root, double target, int k) {
            // 先找到距离target最近的
            TreeNode target_node = root;
            TreeNode temp = root;
            
            for (;;) {
                if (target < temp.val && temp.left != null) {
                    temp = temp.left;
                } else if (target > temp.val && temp.right != null) {
                    temp = temp.right;
                } else {
                    break;
                }
                if (Math.abs(temp.val - target) < Math.abs(target_node.val - target)) target_node = temp;
            }
            
            List<Integer> res = new ArrayList<>();
            // 用类似于 2 pointers 的算法
            int count = 1;
            res.add(target_node.val);
            TreeNode pred = getPred(root, target_node);
            TreeNode succ = getSuc(root, target_node);
            while (count < k) {
                if (pred == null) {
                    res.add(succ.val);
                    succ = getSuc(root, succ);
                    count++;
                } else if (succ == null) {
                    res.add(pred.val);
                    pred = getPred(root, pred);
                    count++;
                }
                else if (Math.abs(pred.val - target) < Math.abs(succ.val - target)) {
                    res.add(pred.val);
                    pred = getPred(root, pred);
                    count++;
                } else {
                    res.add(succ.val);
                    succ = getSuc(root, succ);
                    count++;
                }
            }
            return res;
        }
            
        private TreeNode getPred(TreeNode root, TreeNode target) {
            // 有左子树，则predecessor在左子树，最大的
            if (target.left != null) {
                target = target.left;
                while (target.right != null) {
                    target = target.right;
                }
                return target;
            }
            // 否则，是第一个左祖先
            TreeNode curr = root;
            TreeNode ret = null;
            while (curr != target) {
                if (target.val < curr.val) {
                    curr = curr.left;
                } else {
                    ret = curr;
                    curr = curr.right;
                }
            }
            return ret;
        }
        private TreeNode getSuc(TreeNode root, TreeNode target) {
            // 有右孩子，则successor一定在右子树
            if (target.right != null) {
                target = target.right;
                while (target.left != null) {
                    target = target.left;
                }
                return target;
            }
            // 否则是第一个右祖先
            TreeNode curr = root;
            TreeNode ret = null;
            while (curr != target) {
                if (target.val < curr.val) {
                    ret = curr;
                    curr = curr.left;
                } else {
                    curr = curr.right;
                }
            }
            return ret;
        }
    } 
```

**优化：**
接下来我们注意到之前的code中，每次 get predecessor 和 successor 的过程中最耗时的就是从root开始向下找最近的左祖先或右祖先，每次调用都要运行一遍，毫无疑问是重复的，于是我们就想能否优化这一步骤，答案是可以。

对successor 和 predecessor 分别维持一个stack，这里用 successor 举例：

当我们有如下BST的时候：
![](/assets/WechatIMG30.jpg)

经过优化，获取 predecessor 和 successor 的操作只有一开始的initialize stack 是 O(logN)的，之后每次获取事实上都是 O(1), 因此，时间复杂度变为 O(k + logN).

优化之后的Code：
```java
    class Solution {
        private Deque<TreeNode> succStack;
        private Deque<TreeNode> predStack;
        public List<Integer> closestKValues(TreeNode root, double target, int k) {
            // 先找到距离target最近的
            TreeNode target_node = root;
            TreeNode temp = root;
            while (temp != null) {
                if (target < temp.val) {
                    temp = temp.left;
                } else {
                    temp = temp.right;
                }
                if (temp != null && Math.abs(target_node.val - target) > Math.abs(temp.val - target)) {
                    target_node = temp;
                }
            }        
            
            // 得到predecessor 和 successor stack
            this.succStack = initSuccStack(root, target_node);
            this.predStack = initPredStack(root, target_node);
            
            // 用类似 2 pointers 的方法找k个最近的
            List<Integer> res = new ArrayList<>();
            int count = 1;
            res.add(target_node.val);
            Integer succ = getSucc();
            Integer pred = getPred();
            while (count < k) {
                if (succ == null) {
                    res.add(pred);
                    pred = getPred();
                } else if (pred == null){
                    res.add(succ);
                    succ = getSucc();
                } else if (Math.abs(succ - target) < Math.abs(pred - target)) {
                    res.add(succ);
                    succ = getSucc();
                } else {
                    res.add(pred);
                    pred = getPred();
                }
                count++;
            }
            return res;
        }
        
        private Deque<TreeNode> initSuccStack(TreeNode root, TreeNode target) {
            // 从root开始向下, 把右祖先加入stack
            Deque<TreeNode> stack = new LinkedList<>();
            TreeNode curr = root;
            while (curr != target) {
                if (target.val < curr.val) {
                    stack.addLast(curr);
                    curr = curr.left;
                } else {
                    curr = curr.right;
                }
            }
            // 接下来要注意，和单纯的找successor不同，因为在找右子树最左端的过程中经过的路径都是最左端
            // node 的右祖先，我们需要把它们都压栈
            if (target.right != null) {
                curr = target.right;
                stack.addLast(curr);
                while (curr.left != null) {
                    curr = curr.left;
                    stack.addLast(curr);
                }
            }
            return stack;
        }
        private Deque<TreeNode> initPredStack(TreeNode root, TreeNode target) {
            // 从root向下，把左祖先加入stack
            Deque<TreeNode> stack = new LinkedList<>();
            TreeNode curr = root;
            while (curr != target) {
                if (target.val < curr.val) curr = curr.left;
                else {
                    stack.addLast(curr);
                    curr = curr.right;
                }
            }
            // 因为在找左子树最右端的过程中经过的路径都是最右端
            // node 的左祖先，我们需要把它们都压栈
            if (target.left != null) {
                curr = target.left;
                stack.addLast(curr);
                while (curr.right != null) {
                    curr = curr.right;
                    stack.addLast(curr);
                }
            }
            return stack;
        }
        private Integer getSucc() {
            if (succStack.isEmpty()) return null;
            TreeNode succ = succStack.removeLast();
            int ret = succ.val;
            if (succ.right != null) {
                succ = succ.right;
                succStack.addLast(succ);
                while (succ.left != null) {
                    succ = succ.left;
                    succStack.addLast(succ);
                }
            }
            return ret;
        }
        private Integer getPred() {
            if (predStack.isEmpty()) return null;
            TreeNode pred = predStack.removeLast();
            int ret = pred.val;
            if (pred.left != null) {
                pred = pred.left;
                predStack.addLast(pred);
                while (pred.right != null) {
                    pred = pred.right;
                    predStack.addLast(pred);
                }
            }
            return ret;
        }
    }
```
<br>

---
_update Dec 25, 2017  4:14_

**Python Code:**
```python
class Solution:
    def closestKValues(self, root, target, k):
        """
        :type root: TreeNode
        :type target: float
        :type k: int
        :rtype: List[int]
        """
        def initSucStack(node):
            stack = []
            temp = root
            # add right parent
            while temp is not node:
                if temp.val < node.val:
                    temp = temp.right
                else:
                    stack.append(temp)
                    temp = temp.left
            # add node's right child and left all the way
            temp = node.right
            while temp:
                stack.append(temp)
                temp = temp.left
            return stack
            
        
        def initPredStack(node):
            stack = []
            temp = root
            # add left parent
            while temp is not node:
                if temp.val < node.val:
                    stack.append(temp)
                    temp = temp.right
                else:
                    temp = temp.left
            # add node's left child and all the way right
            temp = node.left
            while temp:
                stack.append(temp)
                temp = temp.right
            return stack
            
        
        def getNextSuc():
            if not sucStack:
                return None
            ret = sucStack.pop()
            temp = ret.right
            while temp:
                sucStack.append(temp)
                temp = temp.left
            return ret.val
            
            
        def getNextPred():
            if not predStack:
                return None
            ret = predStack.pop()
            temp = ret.left
            while temp:
                predStack.append(temp)
                temp = temp.right
            print(ret.val)
            return ret.val
            
        
        
        # find closest first
        temp = root
        node = root
        while True:
            if temp.val < target:
                if not temp.right: break
                temp = temp.right
            else:
                if not temp.left: break
                temp = temp.left
            if abs(temp.val - target) < abs(node.val - target):
                node = temp

        # initialize sucstack and predstack for node
        sucStack = initSucStack(node)
        predStack = initPredStack(node)
        
        # two pointers
        ret = []
        ret.append(node.val)
        k -= 1
        suc, pred = getNextSuc(), getNextPred()
        while k > 0:
            if suc is None:
                ret.append(pred)
                pred = getNextPred()
            elif pred is None:
                ret.append(suc)
                suc = getNextSuc()
            elif abs(target - suc) < abs(target - pred):
                ret.append(suc)
                suc = getNextSuc()
            else:
                ret.append(pred)
                pred = getNextPred()
            k -= 1
        
        return ret
```




