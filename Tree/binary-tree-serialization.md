## Binary Tree Serialization
_update Jul 15, 2017 16:46_

---
[lintcode](http://www.lintcode.com/en/problem/binary-tree-serialization/#)

Design an algorithm and write code to serialize and deserialize a binary tree. Writing the tree to a file is called 'serialization' and reading back from the file to reconstruct the exact same binary tree is 'deserialization'.

** Notice**

There is no limit of how you deserialize or serialize a binary tree, LintCode will take your output of serialize as the input of deserialize, it won't check the result of serialize.

    Example
    An example of testdata: Binary tree {3,9,20,#,#,15,7}, denote the following structure:
    
      3
     / \
    9  20
      /  \
     15   7
Our data serialization use bfs traversal. This is just for when you got wrong answer and want to debug the input.

You can use other method to do serializaiton and deserialization.

#### Basic Idea:

1.  首先是serialization，只要按照level order的顺序遍历，遇null则append “#”即可。serialization 的结果为 something like: `"1,2,3,#,#,#,#"`;
2.  deserialization，可以理解为一个一边建树，一边遍历的过程。从root开始，按照leve order traversal 的顺序遍历，每次从 queue 中取出一个 node 之后，则按照相应的 string 更新其 left 和 right child，完事后再把他的 children enqueue。因为string的顺序和node是从左到右，从上到下一一对应的，所以可以保证正确性。

#### Java Code:
```java
    public class Solution {
        /**
         * This method will be invoked first, you should design your own algorithm 
         * to serialize a binary tree which denote by a root node to a string which
         * can be easily deserialized by your own "deserialize" method later.
         */
        public String serialize(TreeNode root) {
            if (root == null) return "#";
            Deque<TreeNode> queue = new LinkedList<>();
            StringBuilder sb = new StringBuilder();
            queue.addFirst(root);
            while (! queue.isEmpty()) {
                TreeNode node = queue.removeLast();
                if (node == null) sb.append("#,");
                else {
                    sb.append(node.val + ",");
                    queue.addFirst(node.left);
                    queue.addFirst(node.right);
                }
            }
            sb.deleteCharAt(sb.length() - 1);
            return sb.toString();
        }
    
        /**
         * This method will be invoked second, the argument data is what exactly
         * you serialized at method "serialize", that means the data is not given by
         * system, it's given by your own serialize method. So the format of data is
         * designed by yourself, and deserialize it here as you serialize it in 
         * "serialize" method.
         */
        public TreeNode deserialize(String data) {
            if (data.equals("#")) return null;
            String[] str = data.split(",");
            TreeNode root = new TreeNode(Integer.parseInt(str[0]));
            Deque<TreeNode> queue = new LinkedList<>();
            queue.addFirst(root);
            int i = 1;
            while (! queue.isEmpty()) {
                TreeNode node = queue.removeLast();
                if (! str[i].equals("#")) {
                    node.left = new TreeNode(Integer.parseInt(str[i]));
                    queue.addFirst(node.left);
                }
                i++;
                if (! str[i].equals("#")) {
                    node.right = new TreeNode(Integer.parseInt(str[i]));
                    queue.addFirst(node.right);
                }
                i++;
            }
            return root;
        }
    }
```