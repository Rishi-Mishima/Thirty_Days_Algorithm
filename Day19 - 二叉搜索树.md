|                           LeetCode                           |                             力扣                             | 难度 |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :--: |
| [98. Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/) | [98. 验证二叉搜索树](https://leetcode.cn/problems/validate-binary-search-tree/) |      |
| [700. Search in a Binary Search Tree](https://leetcode.com/problems/search-in-a-binary-search-tree/) | [700. 二叉搜索树中的搜索](https://leetcode.cn/problems/search-in-a-binary-search-tree/) |      |
| [701. Insert into a Binary Search Tree](https://leetcode.com/problems/insert-into-a-binary-search-tree/) | [701. 二叉搜索树中的插入操作](https://leetcode.cn/problems/insert-into-a-binary-search-tree/) |      |
| [450. Delete Node in a BST](https://leetcode.com/problems/delete-node-in-a-bst/) | [450. 删除二叉搜索树中的节点](https://leetcode.cn/problems/delete-node-in-a-bst/) |      |

前置知识

阅读本文前，你需要先学习：

- [二叉树结构基础](https://labuladong.online/zh/algo/data-structure-basic/binary-tree-basic/)
- [二叉树的 DFS/BFS 遍历](https://labuladong.online/zh/algo/data-structure-basic/binary-tree-traverse-basic/)



BST 的基础操作主要依赖「左小右大」的特性，可以在二叉树中做类似二分搜索的操作，寻找一个元素的效率很高。比如下面这就是一棵合法的二叉树：

<img src="https://labuladong.online/images/algo/bst/0.png" alt="img" style="zoom:25%;" />



BST 模板

```java
void BST(TreeNode root, int target) {
    if (root.val == target)
        // 找到目标，做点什么
    if (root.val < target) 
        BST(root.right, target);
    if (root.val > target)
        BST(root.left, target);
}
```





### 力扣98. 验证二叉搜索树(HOT100)

注意有坑!

- BST 的每个节点应该要小于右边子树的**所有**节点，下面这个二叉树显然不是 BST，因为节点 `7` 的左子树中有一个节点 `8`，但是我们的算法会把它判定为合法 BST

<img src="/Users/ruixue/Library/Application Support/typora-user-images/image-20260717162623193.png" alt="image-20260717162623193" style="zoom:25%;" />

**错误的原因在于，对于每一个节点 `root`，代码值检查了它的左右孩子节点是否符合左小右大的原则；但是根据 BST 的定义，`root` 的整个左子树都要小于 `root.val`，整个右子树都要大于 `root.val`**。

- 因此需要Root约束

```java
class Solution {
    public boolean isValidBST(TreeNode root) {
        return isValidBST(root, null, null);
    }

    // 限定以 root 为根的子树节点必须满足 max.val > root.val > min.val
    boolean isValidBST(TreeNode root, TreeNode min, TreeNode max) {
        // base case
        if (root == null) return true;
        // 若 root.val 不符合 max 和 min 的限制，说明不是合法 BST
        if (min != null && root.val <= min.val) return false;
        if (max != null && root.val >= max.val) return false;
        // 限定左子树的最大值是 root.val，右子树的最小值是 root.val
        return isValidBST(root.left, min, root)
                && isValidBST(root.right, root, max);
    }
}
```



````java
二叉搜索树(BST) 
- 3个力扣题

力扣98. 验证二叉搜索树(HOT100)
- 注意有坑
- BST 的每个节点应该要小于右边子树的**所有**节点, 但是还必须要验证ROOT

> BST 的定义，`root` 的整个左子树都要小于 `root.val`，整个右子树都要大于 `root.val

```java
if (min != null && root.val <= min.val) return false;
        if (max != null && root.val >= max.val) return false;
```
````

