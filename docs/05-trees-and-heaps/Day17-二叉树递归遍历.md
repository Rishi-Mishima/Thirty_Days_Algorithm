| 题号 | 题目                    | 知识点   |
| ---- | ----------------------- | -------- |
| 104  | Maximum Depth           | DFS      |
| 226  | Invert Binary Tree      | 递归     |
| 543  | Diameter of Binary Tree | 后序遍历 |
| 110  | Balanced Binary Tree    | 后序遍历 |
| 112  | Path Sum                | DFS      |
| 113  | Path Sum II             | DFS+回溯 |
| 124  | Maximum Path Sum        | 后序遍历 |
| 236  | Lowest Common Ancestor  | 递归     |
| 199  | Right Side View         | BFS      |
| 102  | Level Order Traversal   | BFS      |
| 114  | Flatten Binary Tree     | 递归     |

### 力扣树题学习计划

**第一周：遍历与递归基础**

- 94 中序遍历
- 144 前序遍历
- 145 后序遍历
- 104 最大深度
- 226 翻转二叉树
- 100 相同的树
- 101 对称二叉树

目标：真正理解递归、前中后序遍历分别适合解决什么问题。

**第二周：DFS + 回溯 + BFS**

- 102 层序遍历
- 199 二叉树的右视图
- 543 二叉树的直径
- 110 平衡二叉树
- 112 路径总和
- 113 路径总和 II

目标：掌握队列层序遍历、树上的回溯以及“递归返回值”和“全局变量”两类解法。

**第三周：BST 与综合高频题**

- 98 验证二叉搜索树
- 230 二叉搜索树中第 K 小的元素
- 236 二叉树的最近公共祖先
- 105 从前序与中序遍历序列构造二叉树
- 114 二叉树展开为链表
- 124 二叉树中的最大路径和
- 437 路径总和 III



#### 力扣226. 反转二叉树

> invertTree(root) 的作用：把以 root 为根节点的整棵子树翻转，并返回翻转后的根节点。

递归会一直往下走

交换之前，先翻转左右子树。

所以这是：

> **后序遍历（Left → Right → Root）**

```java
TreeNode left = TreeNode(root.left);

不是得到原来的左子树, 而是已经翻转完成的左子树
```

永远表示：

> **翻转完成后的左子树根节点。**

这就是递归最大的特点：

> **相信递归函数已经完成了它承诺的工作。**

你不需要关心它内部怎么翻，只需要知道：

- `invertTree(root.left)` 返回的是**已经翻转好的左子树**。
- `invertTree(root.right)` 返回的是**已经翻转好的右子树**。



##### 答案

```java
// 定义：将以 root 为根的这棵二叉树翻转，返回翻转后的二叉树的根节点
public TreeNode invertTree(TreeNode root) {
    if (root == null) {
        return null;
    }
    // 利用函数定义，先翻转左右子树
    TreeNode left = invertTree(root.left);
    TreeNode right = invertTree(root.right);

    // 然后交换左右子节点
    root.left = right;
    root.right = left;
    
    return root;
}
```



#### 力扣116. 填充每个二叉树节点的右侧指针

没在力扣HOT100 



#### 力扣114. 将二叉树展开为链表

##### 方法1: 前序遍历, 一边遍历一边构造出一条链表? 

但是注意 `flatten` 函数的签名，返回类型为 `void`，也就是说题目希望我们==在原地把二叉树拉平成链表==。

这样一来，没办法通过简单的二叉树遍历来解决这道题了。

1. **外部变量**：
   - `dummy` 是一个虚拟头节点，用于简化链表操作。
     - dummy.right
   - `p` 是一个指针，始终指向当前构建好的链表的末尾节点。
     - 链表的最后一个节点
2. **前序遍历**：
   - 在 `traverse` 函数中，按照 **根 -> 左 -> 右** 的顺序访问节点。
   - `p.right = new TreeNode(root.val)`：创建一个新节点（值与当前节点相同），并将其挂在 `p` 的右边。
   - `p = p.right`：将 `p` 指针移动到这个新节点上，使其成为新的末尾。
3. **结果**：
   - 遍历结束后，`dummy.right` 指向的就是按照先序遍历顺序排列的新链表。

```java
// 虚拟头节点，dummy.right 就是结果
TreeNode dummy = new TreeNode(-1);
// 用来构建链表的指针
TreeNode p = dummy;

void traverse(TreeNode root) {
    if (root == null) {
        return;
    }
    // 前序位置
    p.right = new TreeNode(root.val);
    p = p.right;

    traverse(root.left);
    traverse(root.right);
}
```



我的疑问

```markdown
> p.right = new TreeNode(root.val); p = p.right; 为什么不是p.left 难道不应该前序遍历先走左边吗

p 已经不是树节点了，它是在构建链表

p.right 它表示的是：链表最后一个节点。

前序遍历真正的先左后右是在: 
 traverse(root.left);
	traverse(root.right);
```





##### 方法二: 分解问题? 

对于一个节点 `x`，可以执行以下流程：

1、先利用 `flatten(x.left)` 和 `flatten(x.right)` 将 `x` 的左右子树拉平。

2、将整个左子树作为右子树，然后将原先的右子树接到当前右子树的末端。

<img src="https://labuladong.online/images/algo/binary-tree-i/2.jpeg" alt="img" style="zoom: 33%;" />



用后序遍历: 

`flatten(root.left)` 和 `flatten(root.right)` 是递归调用，它们的作用是把左子树和右子树分别拉成链表。只有等这两个递归调用返回后，`root.left` 和 `root.right` 指向的才是已经拉平的链表。

此时才能在**后序位置**执行后续操作：

1. 把左链表接到 `root.right`。
2. 找到左链表的末尾，把原来的右链表接上去。

如果在前序或中序位置做，左右子树还没被拉平，你就拿不到正确的链表结构，无法完成拼接。

所以必须用后序遍历：**先解决子问题（拉平左右子树），再处理当前节点（拼接链表）**。



```java
TreeNode left = root.left;   // 保存原来的左子树
TreeNode right = root.right; // 保存原来的右子树
```

这两行代码的作用是**暂存**当前节点 `root` 的左右子节点。

因为在接下来的步骤中，我们需要修改 `root.left` 和 `root.right` 的指向（把左子树挪到右边），如果不先保存原来的引用，一旦修改了指针，就找不到原来的右子树了。



**修改指针**（将左子树置空，并把刚才保存的 `left` 接到右边）：

```java
root.left = null; 

root.right = left; 
```

执行完 `root.right = left` 后，树变成了：

```
1
 \
  2
   \
    3
     \
      4
      
      （注意：此时 1 的右边是 2，但 5 还没接上来）
```

`while` 循环会让 `p` 走到节点 `4`（因为 `4.right` 是 `null`）。

执行 `p.right = right` 后，节点 `4` 的右边连上了 `5`



**连接右子树**（找到当前右子树的末尾，把刚才保存的 `right` 接上去）：

```java
TreeNode p = root; 
while(p.right != null){
  p = p.right;
}
p.right = right; 
```

 这段代码的目的是**找到当前右子树（即原来的左子树）的最后一个节点，然后把原来的右子树接在后面**。

因为题目要求展开后的链表顺序必须和**先序遍历**一致（根 -> 左 -> 右），所以在把左子树挪到右边之后，原来的右子树必须排在左子树的后面。

1. **`TreeNode p = root;`** 创建一个指针 `p`，从当前节点 `root` 开始出发。此时 `root.right` 指向的是刚才移过来的“原左子树”。
2. **`while (p.right != null) { p = p.right; }`** 沿着右指针一直往下走，直到找到这条链表的**末尾节点**（即最右下角的节点）。
   - 为什么要找末尾？因为我们要把“原右子树”接在整个“原左子树”的后面，而不是直接覆盖掉 `root.right`。
3. **`p.right = right;`** 将末尾节点的 `right` 指针指向之前保存的“原右子树”（即变量 `right`）。



##### 答案1: 先把左右二叉树都拉平, 然后拼接

```java
class Solution {
    // 定义：将以 root 为根的树拉平为链表
    public void flatten(TreeNode root) {
        // base case
        if (root == null) return;
        
        // 利用定义，把左右子树拉平
        flatten(root.left);
        flatten(root.right);

        // *** 后序遍历位置 ***
        // 1、左右子树已经被拉平成一条链表
        TreeNode left = root.left;
        TreeNode right = root.right;
        
        // 2、将左子树作为右子树
        root.left = null;
        root.right = left;

        // 3、将原先的右子树接到当前右子树的末端
        TreeNode p = root;
        while (p.right != null) {
            p = p.right;
        }
        p.right = right;
    }
}
```

