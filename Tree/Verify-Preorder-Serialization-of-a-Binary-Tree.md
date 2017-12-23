## Verify Preorder Serialization of a Binary Tree
_update Dec 22, 2017  19:32_

---
One way to serialize a binary tree is to use pre-order traversal. When we encounter a non-null node, we record the node's value. If it is a null node, we record using a sentinel value such as `#`.
          
               _9_
              /   \
             3     2
            / \   / \
           4   1  #  6
          / \ / \   / \
          # # # #   # #
For example, the above binary tree can be serialized to the string `"9,3,4,#,#,1,#,#,2,#,6,#,#"`, where `#` represents a null node.

**Given a string of comma separated values, verify whether it is a correct preorder traversal serialization of a binary tree. Find an algorithm without reconstructing the tree.**

Each comma separated value in the string must be either an integer or a character `'#'` representing null pointer.

You may assume that the input format is always valid, for example it could never contain two consecutive commas such as `"1,,3"`.

          Example 1:
          "9,3,4,#,#,1,#,#,2,#,6,#,#"
          Return true
          
          Example 2:
          "1,#"
          Return false
          
          Example 3:
          "9,#,#,1"
          Return false

#### Basic Idea:
先看 **[这里](https://www.hrwhisper.me/leetcode-verify-preorder-serialization-of-a-binary-tree/)** 有两种最好方法的讲解。

##### 思路 1 （stack，一次去掉叶子节点）：
我们每次去掉叶子节点，即 `“\d##”`，将其换成一个空节点`“#”`，那么如果所给string是valid的，最终我们应该只剩下 `“#”`。实现的过程就是使用一个stack，每次当有三个以上元素的时候检查倒数三个元素是否表示一个叶子节点，若是的话将其换成 `"#"`，最后检查是否只剩 `"#"`；

&emsp; **Python Code:**
```python
    class Solution:
        def isValidSerialization(self, preorder):
            """
            :type preorder: str
            :rtype: bool
            """
            lst = preorder.split(",")
            stack = []
            for val in lst:
                stack.append(val)
                while len(stack) >= 3 and stack[-2:] == ["#", "#"] and stack[-3] != "#":
                    stack = stack[:-3] + ["#"]
                
            if len(stack) == 1 and stack[0] == "#": 
                return True
            else: return False
```

##### 思路 2 （统计 indegree and outdegree):
&emsp;在一个二叉树中，首先我们把叶子的左右孩子 nil 视为一个节点。一个正常节点提供一个 indegree 和 两个 outdegree，根节点 root 提供两个 outdegree，一个 nil 提供一个 indegree。那么在任何时候，**二叉树的 indegree 必须小于等于 outdegree，并且最终两者必须相等**。

这个性质的物理意义可以这样描述：<sp>
&emsp; outdegree 大于 indegree 表示仍有空余的 outdegree 可以被用于连接更多的node，如果入度大于出度，就说明出现了来源不明的入度（每个入度一定是连接在另一个 node 的出边上，抵消一个出度），此时就可以判断为 invalid;

&emsp; **Python Code:**
```python


##### 思路3 (不符合题意)：
recursively 重建整个树，检查是否刚好用完所给 string 中的所有元素，如果提前用完或者建树之后仍有剩余，则说明所给string有问题。

**Python Code:**
```python
    class Solution:
        def isValidSerialization(self, preorder):
            """
            :type preorder: str
            :rtype: bool
            """
            def helper():
                val = next(data)
                if val == "#": return None
                node = TreeNode(int(val))
                node.left = helper()
                node.right = helper()
                return node
                
                
            data = iter(preorder.split(","))
            # case 1: 在deserialization过程中，有node需要左右node的时候出现stopiteration，
            # 则说明有错误
            try : helper()
            except StopIteration: return False
            # case 2：deserialization结束之后，如果仍有string没有处理，也表示有问题
            try: next(data)
            except StopIteration: return True
            return False
```















