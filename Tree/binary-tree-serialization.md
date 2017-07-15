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
1.  首先是serialization，只要按照level order的顺序遍历，遇null则append “#”即可。
2.  deserialization，可以理解为一个一边建树，一边遍历的过程。从root开始，每次按照给定string建立node的左右孩子，然后把他们enque，再继续。使用isleft变量跟踪当前应该是做孩子还是右孩子。

#### Java Code:
```java
class Solution {
    /**
     * This method will be invoked first, you should design your own algorithm 
     * to serialize a binary tree which denote by a root node to a string which
     * can be easily deserialized by your own "deserialize" method later.
     */
     
    // 正常level order traversial的顺序，不论有无左右孩子都enque，如果处理的是null
    // 则在结果中append "#"
    public String serialize(TreeNode root) {
        if (root == null) return "#";
        Deque<TreeNode> queue = new LinkedList<>();
        List<String> buffer = new ArrayList<>();
        queue.addFirst(root);
        while (queue.size() > 0) {
            TreeNode node = queue.removeLast();
            if (node == null) {
                buffer.add("#");
                continue;
            }
            else buffer.add(node.val + "");
            queue.addFirst(node.left);
            queue.addFirst(node.right);
        }
        while (buffer.get(buffer.size() - 1).equals("#")) buffer.remove(buffer.size() - 1);
        StringBuilder sb = new StringBuilder();
        for (String val : buffer) {
            sb.append(val + ",");
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
     
    // 每次根据当前val给当前node建立左右child，然后把child enque，一边建，一边traverse
    public TreeNode deserialize(String data) {
        if (data.equals("#")) return null;
        String[] vals = data.split(",");
        TreeNode root = new TreeNode(Integer.parseInt(vals[0]));
        Deque<TreeNode> queue = new LinkedList<>();
        queue.addFirst(root);
        boolean isLeft = true;
        for (int i = 1; i < vals.length; ++i) {
            TreeNode node = queue.peekLast();
            if (! vals[i].equals("#")) {
                if (isLeft) {
                    node.left = new TreeNode(Integer.parseInt(vals[i]));
                    queue.addFirst(node.left);
                    isLeft = false;
                } else {
                    node.right = new TreeNode(Integer.parseInt(vals[i]));
                    queue.addFirst(node.right);
                    queue.removeLast();
                    isLeft = true;
                }
            } else {
                isLeft ^= true;
                if (isLeft) queue.removeLast();
            }
        }
        return root;
    }
}
```