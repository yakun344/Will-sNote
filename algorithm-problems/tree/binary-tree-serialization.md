# Binary Tree Serialization

_update Jul 15, 2017 16:46_

[lintcode](http://www.lintcode.com/en/problem/binary-tree-serialization/#)\
[LeetCode](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/description/)

Design an algorithm and write code to serialize and deserialize a binary tree. Writing the tree to a file is called 'serialization' and reading back from the file to reconstruct the exact same binary tree is 'deserialization'.

** Notice**

There is no limit of how you deserialize or serialize a binary tree, LintCode will take your output of serialize as the input of deserialize, it won't check the result of serialize.

```
Example
An example of testdata: Binary tree {3,9,20,#,#,15,7}, denote the following structure:

  3
 / \
9  20
  /  \
 15   7
```

Our data serialization use bfs traversal. This is just for when you got wrong answer and want to debug the input.

You can use other method to do serializaiton and deserialization.

### Basic Idea:

1. 首先是serialization，只要按照level order的顺序遍历，遇null则append “#”即可。serialization 的结果为 something like: `"1,2,3,#,#,#,#"`;
2. deserialization，可以理解为一个一边建树，一边遍历的过程。从root开始，按照leve order traversal 的顺序遍历，每次从 queue 中取出一个 node 之后，则按照相应的 string 更新其 left 和 right child，完事后再把他的 children enqueue。因为string的顺序和node是从左到右，从上到下一一对应的，所以可以保证正确性。
3.  这里的实现方法没有去掉最后的一连串 `"#,#,#,# ···"`，如有需要可以优化。

    **Java Code:**

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

_update Dec 22, 2017 18:49_

## Update: Preorder Serialization

更新一种新的 serilization 的方法，preorder serilization。和之前使用BFS（level order traversal）序列化的方法有所区别，但思想上又异曲同工：BFS的序列化和还原都是类似的方法，同样的，preorder 的序列化和还原也可以用 recursive 的 preorder 的方法来实现。

这里用 iterator 来代替原本的 String 数组，如果使用数组，则需要多传入一个index参数。

**Java Code:**

```java
    public class Solution {
        /**
         * This method will be invoked first, you should design your own algorithm
         * to serialize a binary tree which denote by a root node to a string which
         * can be easily deserialized by your own "deserialize" method later.
         */
        public String serialize(TreeNode root) {
            if (root == null) return "#";
            StringBuilder sb = new StringBuilder();
            preOrder(root, sb);
            sb.deleteCharAt(sb.length() - 1);
            System.out.println(sb.toString());
            return sb.toString();
        }

        private void preOrder(TreeNode root, StringBuilder sb) {
            if (root == null) {
                sb.append("#,");
            } else {
                sb.append(root.val + ",");
                preOrder(root.left, sb);
                preOrder(root.right, sb);
            }
        }

        /**
         * This method will be invoked second, the argument data is what exactly
         * you serialized at method "serialize", that means the data is not given by
         * system, it's given by your own serialize method. So the format of data is
         * designed by yourself, and deserialize it here as you serialize it in
         * "serialize" method.
         */
        public TreeNode deserialize(String data) {
            String[] arr = data.split(",");
            Iterator<String> iter = Arrays.asList(arr).iterator();
            return helper(iter);
        }

        private TreeNode helper(Iterator<String> iter) {
            if (! iter.hasNext()) return null;
            String str = iter.next();
            if (str.equals("#")) return null;
            TreeNode node = new TreeNode(Integer.parseInt(str));
            node.left = helper(iter);
            node.right = helper(iter);
            return node;
        }
    }
```

_update 2018-05-27 23:30:07_

## Update: C++ BFS Solution

C++的实现和前面的Java实现大同小异，不同点在于：

1. C++ 中如何 split string： 使用 stringstream;
2. C++ 中如何从 string 中 parse int： 使用 std::stoi(string);

```cpp
  class Codec {
  public:
      // Encodes a tree to a single string.
      string serialize(TreeNode* root) {
          if (root == nullptr) return "#";
          string res;
          deque<TreeNode*> _queue;
          _queue.push_back(root);
          while (! _queue.empty()) {
              TreeNode* node = _queue.front();
              _queue.pop_front();
              if (node == nullptr) {
                  res.append("#,");
              } else {
                  res.append(to_string(node->val) + ",");
                  _queue.push_back(node->left);
                  _queue.push_back(node->right);
              }
          }
          cout << res;
          return res.substr(0, res.size() - 1);
      }

      // Decodes your encoded data to tree.
      TreeNode* deserialize(string data) {
          if (data == "#") return nullptr;
          stringstream ss(data);
          string buffer;
          vector<string> token;
          while (getline(ss, buffer, ',')) {
              token.push_back(buffer);
          }
          deque<TreeNode*> _queue;
          TreeNode* root = new TreeNode(stoi(token[0]));
          _queue.push_back(root);
          int i = 1;
          while (! _queue.empty()) {
              TreeNode* node = _queue.front();
              _queue.pop_front();
              string leftString = token[i++];
              string rightString = token[i++];
              if (leftString != "#") {
                  node->left = new TreeNode(stoi(leftString));
                  _queue.push_back(node->left);
              }
              if (rightString != "#") {
                  node->right = new TreeNode(stoi(rightString));
                  _queue.push_back(node->right);
              }
          }
          return root;
      }
  };
```
