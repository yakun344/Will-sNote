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
##### 思路1 (不符合题意)：
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

##### 思路2：
题目中有要求不可以重建整个树，所以第一种方法不可以。我们需要利用preorder的一些性质进行判断。














