# Tweaked Identical Binary Trees

_update Mar 3, 2018 23:34_

Determine whether two given binary trees are identical assuming any number of ‘tweak’s are allowed. A tweak is defined as a swap of the children of one node in the tree.

**Examples**

```text
            5

          /    \

        3        8

      /   \

    1      4
```

and

```text
      5

    /    \

  8        3

         /   \

        1     4
```

the two binary trees are tweaked identical.

## Basic Idea:

递归求解，对于每对node，考虑 1左2左 1右2右，1左2右 1右2左 两种情况。

* **Java Code:**

  ```java
  public class Solution {
    public boolean isTweakedIdentical(TreeNode one, TreeNode two) {
      if (one == null && two == null) return true;
      else if (one == null || two == null) return false;
      else if (one.key != two.key) return false;
      return isTweakedIdentical(one.left, two.left) && isTweakedIdentical(one.right, two.right)
        || isTweakedIdentical(one.left, two.right) && isTweakedIdentical(one.right, two.left);
    }
  }
  ```

