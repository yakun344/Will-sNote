## Lowest Common Ancestor II
_update Aug 27, 2017  22:06_

---
[LintCode](http://www.lintcode.com/en/problem/lowest-common-ancestor-ii/)

Given the root and two nodes in a Binary Tree. Find the lowest common ancestor(LCA) of the two nodes.

The lowest common ancestor is the node with largest depth which is the ancestor of both nodes.

The node has an extra attribute parent which point to the father of itself. The root's parent is null.

**Example**

      For the following binary tree:
      
        4
       / \
      3   7
         / \
        5   6
      LCA(3, 5) = 4
      
      LCA(5, 6) = 7
      
      LCA(6, 7) = 7

#### Basic Idea:
由于这道题的二叉树给了父节点，我们可以从A，B开始往上直到root，分别得到两个链表，如果A，B的公共节点存在，那么就相当于两个链表有共同部分（链表分叉）。此时我们只要从root开始向下，比较每个同样位置的元素，直到下一个开始分叉位置。

#### Java Code:
```java
    public class Solution {
        /**
         * @param root: The root of the tree
         * @param A, B: Two node in the tree
         * @return: The lowest common ancestor of A and B
         */
        public ParentTreeNode lowestCommonAncestorII(ParentTreeNode root,
                                                     ParentTreeNode A,
                                                     ParentTreeNode B) {
            List<ParentTreeNode> lstA = new ArrayList<>();
            List<ParentTreeNode> lstB = new ArrayList<>();
            ParentTreeNode temp = A;
            while (temp != null) {
                lstA.add(temp);
                temp = temp.parent;
            }
            temp = B;
            while (temp != null) {
                lstB.add(temp);
                temp = temp.parent;
            }
            int len = Math.min(lstA.size(), lstB.size());
            for (int i = 0; i < len; ++i) {
                if (lstA.get(lstA.size() - 1) == lstB.get(lstB.size() - 1)) {
                    temp = lstA.remove(lstA.size() - 1);
                    lstB.remove(lstB.size() - 1);
                }    
            }
            return temp;
        }
    }
```

#### Python Code:
```python
    class Solution:
        """
        @param root: The root of the tree
        @param A and B: Two node in the tree
        @return: The lowest common ancestor of A and B
        """ 
        def lowestCommonAncestorII(self, root, A, B):
            if A == B:
                return A
            lstA = []
            lstB = []
            while A:
                lstA.append(A)
                A = A.parent
            while B:
                lstB.append(B)
                B = B.parent
            temp = root
            length = min(len(lstA), len(lstB))
            for i in range(length):
                if lstA[-1] == lstB[-1]:
                    temp = lstA.pop()
                    lstB.pop()
            return temp
```   
<br>

---
_update Dec 20, 2017 1:04_

### Update
更简单的方法是使用 HashSet，先把第一条链表每个node加入set，然后依次验证第二条链表的node是否在其中，第一个在 set 中的就是分叉节点；

**Python Code:**   
    ```python
    class Solution:
        """
        @param: root: The root of the tree
        @param: A: node in the tree
        @param: B: node in the tree
        @return: The lowest common ancestor of A and B
        """
        def lowestCommonAncestorII(self, root, A, B):
            st = set()
            while A:
                st.add(A)
                A = A.parent
            while B:
                if B in st: 
                    return B
                B = B.parent
```

**Java Code:**
```java
    public class Solution {
        /*
         * @param root: The root of the tree
         * @param A: node in the tree
         * @param B: node in the tree
         * @return: The lowest common ancestor of A and B
         */
        public ParentTreeNode lowestCommonAncestorII(ParentTreeNode root, ParentTreeNode A, ParentTreeNode B) {
            Set<ParentTreeNode> set = new HashSet<>();
            while (A != null) {
                set.add(A);
                A = A.parent;
            }
            while (B != null) {
                if (set.contains(B)) return B;
                B = B.parent;
            }
            return null;
        }
    }
```