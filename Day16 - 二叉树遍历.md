# 二叉树遍历

```markdown
二叉树遍历
- 前序
- 中序
- 后序

递归遍历 VS 层序遍历
BFS VS DFS
```







|                           LeetCode                           |                             力扣                             | 难度 |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :--: |
| [144. Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/) | [144. 二叉树的前序遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal/) |      |
| [94. Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/) | [94. 二叉树的中序遍历](https://leetcode.cn/problems/binary-tree-inorder-traversal/) |      |
| [145. Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/) | [145. 二叉树的后序遍历](https://leetcode.cn/problems/binary-tree-postorder-traversal/) |      |
| [102. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/) | [102. 二叉树的层序遍历](https://leetcode.cn/problems/binary-tree-level-order-traversal/) |      |

二叉树只有**递归遍历**和**层序遍历**这两种，再无其他。递归遍历可以衍生出 DFS 算法，层序遍历可以衍生出 BFS 算法。

递归遍历二叉树节点的顺序是固定的，但是有三个关键位置，在不同位置插入代码，会产生不同的效果。

层序遍历二叉树节点的顺序也是固定的，但是有三种不同的写法，对应不同的场景。

<img src="/Users/ruixue/Library/Application Support/typora-user-images/image-20260714151132233.png" alt="image-20260714151132233" style="zoom:50%;" />



### 二叉树类型

<img src="https://labuladong.online/images/algo/complete_tree/trees.png" alt="img"  />



### 递归遍历(DFS)

模板

```java
// 基本的二叉树节点
class TreeNode {
    int val;
    TreeNode left, right;
}

// 二叉树的递归遍历框架
void traverse(TreeNode root) {
    if (root == null) {
        return;
    }
    traverse(root.left);
    traverse(root.right);
}
```

> 遍历顺序不变

可以把 `traverse` 函数想象成一个在树上移动的指针：

- **终止条件**：`if (root == null) return;` 确保指针遇到空节点时停止，不会无限递归。
- **移动逻辑**：先调用 `traverse(root.left)`，指针会一直向左走，直到左边走不通（遇到 null）；然后回溯，再调用 `traverse(root.right)`，向右走一步。
- **递归本质**：对每个非空节点，都重复“先左后右”的过程，从而覆盖整棵树。



2. 遍历顺序是固定的

只要保持 `traverse(root.left)` 在前、`traverse(root.right)` 在后，指针在树上的**游走路径**就是固定的：

> 一直向左 -> 走不动了向右一步 -> 再一直向左 -> ... -> 左右都走完返回父节点。



3. “前/中/后序”的区别在哪里？

虽然游走路径固定，但我们在**不同位置插入代码**，就会产生不同的效果：

```java
void traverse(TreeNode root) {
    if (root == null) return;

    // 【前序位置】：刚进入节点时执行
    System.out.println("前序: " + root.val);

    traverse(root.left);

    // 【中序位置】：左子树遍历完，准备遍历右子树时执行
    System.out.println("中序: " + root.val);

    traverse(root.right);

    // 【后序位置】：左右子树都遍历完，即将离开节点时执行
    System.out.println("后序: " + root.val);
}
```

- **前序**：第一次遇到节点就处理。
- **中序**：左子树处理完后才处理当前节点。
- **后序**：左右子树都处理完后才处理当前节点。

> 每个节点都有自己的前中后序

前中后序: 

- 每个位置节点当时所掌握的信息是不同的

- 前序是知道父节点
  - 节点变绿的顺序，就是前序遍历的结果，因为前序位置的代码是刚进入节点时执行的，所以前序遍历的顺序就是 `root` 指针在树上移动的顺序
- 中序节点可以知道已经处理的子节点(左)和父节点
  - 中序位置的代码是左子树遍历完成后，还未遍历右子树时执行的。
  - 一个节点在它的左子树遍历完时才会变：
- 后序知道最多(左右子节点)和父节点
  - 后序位置的代码是左右子树都遍历完，即将离开节点时执行的。

> 三种位置的关键区别在于执行时机不同。
>
> 实际的算法题中不会简单的让你计算前中后序的遍历结果，而是需要你把正确的代码写到正确的位置，所以你必须准确理解三个位置的代码产生的不同效果，才能写出准确的代码。

`traverse` 函数的遍历顺序就是一直往左子节点走，直到遇到空指针不能再走了，才尝试往右子节点走一步；然后再一直尝试往左子节点走，如此循环；如果左右子树都走完了，则返回上一层父节点。

看代码也能看出来，先递归调用的 `root.left`，然后才递归调用的 `root.right`，每次进入 `traverse` 函数，都会先往左子节点递归遍历，直到遇到空指针走不动了，才轮到往右子节点走一次。



#### 前中后序模板

```java
// 二叉树的遍历框架
void traverse(TreeNode root) {
    if (root == null) {
        return;
    }
    // 前序位置
    traverse(root.left);
    // 中序位置
    traverse(root.right);
    // 后序位置
}
```



现在，你应该可以完成力扣的 [144. 二叉树的前序遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal/)、[94. 二叉树的中序遍历](https://leetcode.cn/problems/binary-tree-inorder-traversal/)、[145. 二叉树的后序遍历](https://leetcode.cn/problems/binary-tree-postorder-traversal/) 这三道题了。

最后一个知识点，[二叉搜索树（BST）](https://labuladong.online/zh/algo/data-structure-basic/binary-tree-basic/) 的中序遍历结果是有序的，这是 BST 的一个重要性质。

可以看这个可视化面板，点击其中 这一行代码，就可以看到中序遍历访问节点的顺序：在后面的 [BST 相关习题集](https://labuladong.online/zh/algo/problem-set/bst1/) 中，会有一些题目利用到这个特性。



### 层序遍历(BFS)

上面讲的递归遍历是依赖函数堆栈递归遍历二叉树的，遍历顺序是从最左侧开始，一列一列地走到最右侧。

二叉树的层序遍历，顾名思义，就是一层一层地遍历二叉树：

<img src="https://labuladong.online/images/algo/dijkstra/1.jpeg" alt="img" style="zoom:33%;" />

层序遍历需要借助队列来实现，而且根据不同的需求，可以有三种不同的写法，下面一一列举。

简单来说，它的核心逻辑就是：**从上到下、从左到右，一层一层地访问二叉树的每一个节点。**



#### 写法一

```java
void levelOrderTraverse(TreeNode root) {
  
  	//边界处理 这棵树是空的（连根节点都没有）直接结束函数
    if (root == null) {
        return;
    }
  
  	// 初始化队列
  	// 创建好队列，先把最顶端的根节点（root）送进去排队。这时候，第一层（根节点）就准备就绪了。
    Queue<TreeNode> q = new LinkedList<>();
    q.offer(root);
  
  	// 循环遍历
    while (!q.isEmpty()) {
        TreeNode cur = q.poll(); // 1. 出队
        // 访问 cur 节点
        System.out.println(cur.val); // 2. 访问

        // 把 cur 的左右子节点加入队列 - // 3. 顺藤摸瓜：把下一层拉进来排队
        if (cur.left != null) {
            q.offer(cur.left);
        }
        if (cur.right != null) {
            q.offer(cur.right);
        }
    }
}
```

这段代码实现的是二叉树的**层序遍历**（Level-Order Traversal），通常也被称为**广度优先搜索**（BFS, Breadth-First Search）。

这段代码能实现“一层层维护顺序”的关键，在于使用了**队列（Queue）**。 队列的特点是 **“先进先出”（FIFO, First In First Out）**，就像排队买票一样，先排队的人先买到票离开。

- `q.offer(item)`：让一个节点去队尾排队（入队）。
- `q.poll()`：让排在最前面的节点离开并处理它（出队）

它利用**队列（Queue）**先进先出（FIFO）的特性，像波纹扩散一样一层一层地访问节点：

1. **初始化**：将根节点 `root` 加入队列。
2. **循环处理**：只要队列不为空，就取出队头节点 `cur`。
3. **访问与扩展**：
   - 访问当前节点 `cur`（代码中是打印值）。
   - 如果 `cur` 有左子节点，将其加入队列尾部。
   - 如果 `cur` 有右子节点，将其加入队列尾部。

只要队列里还有人在排队（`!q.isEmpty()`），循环就不会停止。在每次循环里，它只干三件事：

1. **拿出来：** 从队头吐出一个节点，记作 `cur`（当前节点）。
2. **打印它：** 打印这个节点的值。
3. **留后路：** 看看这个 `cur` 有没有左孩子和右孩子。如果有，就把它们**按从左到右的顺序**送到队尾去排队。因为它们排在队尾，所以一定会等当前这一层的所有节点都处理完，才轮到它们。



优缺点分析

- **优点**：代码极其简洁，逻辑直观。
- **缺点**：**无法记录节点所在的层数（深度）**。因为所有节点都混在同一个队列里，你只知道谁先谁后，但不知道它们具体属于第几层。

适用场景

如果你只需要按顺序访问所有节点，而不关心节点在第几层（例如只是简单地打印所有值），可以用这种写法。



#### 写法二

```java
void levelOrderTraverse(TreeNode root) {
    if (root == null) {
        return;
    }
    Queue<TreeNode> q = new LinkedList<>();
    q.offer(root);
    // 记录当前遍历到的层数（根节点视为第 1 层）
    int depth = 1;

    while (!q.isEmpty()) {
        int sz = q.size();  //// 1. 锁死当前层的节点个数
        for (int i = 0; i < sz; i++) { // // 2. 精准消费这 sz 个节点
            TreeNode cur = q.poll();
            // 访问 cur 节点，同时知道它所在的层数
            System.out.println("depth = " + depth + ", val = " + cur.val);

            // 把 cur 的左右子节点加入队列
            if (cur.left != null) {
                q.offer(cur.left);
            }
            if (cur.right != null) {
                q.offer(cur.right);
            }
        }
        depth++; // // 3. 这一层消费完了，层数加一
    }
```

如果说前一版代码只是“糊里糊涂地把所有人排成一队依次处理”**，那么这一版代码就学会了**“在排队时给每个人划分班级（层级）”。

它最大的变化是：**能够明确知道当前访问的节点属于二叉树的第几层（`depth`）**。

1. **数人数（`int sz = q.size()`）：** 每次准备处理新的一层时，先数一下当前队列里一共有多少个节点。**这个数字 `sz`，就是当前这一层的所有节点数量。**
2. **定点消费（`for` 循环 `sz` 次）：** 用一个 `for` 循环，**雷打不动地只弹出 `sz` 个节点**。
   - 虽然我们在弹出节点的同时，会把它们的子节点（下一层）放入队列，但因为 `for` 循环的次数已经固定成了 `sz`，所以**新加入的节点绝对不会在本次 `for` 循环中被弹出来**。它们只能乖乖在后面排队，等下一轮。
3. **层数递增（`depth++`）：** 当 `for` 循环结束，意味着**当前这一层的所有节点已经被刚好全部消灭**。这时，队列里剩下的全都是下一层的节点了。于是我们把 `depth` 加 1，进入下一轮 `while` 循环。



小细节: 

- 也可以写成: 

```java
int sz = q.size();
while (sz-- > 0) {
    ...
}
```

**为什么要用这个升级版？**

很多二叉树/图论的算法题，不仅要求你遍历节点，还要求你做**跟层数、距离相关**的计算。 比如：

- 让你求“二叉树的最大深度”
- 让你求“二叉树每一层的平均值”
- 让你“把二叉树的每一层单独装进一个 List 里（LeetCode 102 题）”

只要遇到这类**“需要分层处理”**的题目，这个配合 `q.size()` 和 `for` 循环的模版，就是你的标准解题武器！



#### 写法三

现在我们只是在探讨二叉树的层序遍历，但是二叉树的层序遍历可以衍生出 [多叉树的层序遍历](https://labuladong.online/zh/algo/data-structure-basic/n-ary-tree-traverse-basic/)，[图的 BFS 遍历](https://labuladong.online/zh/algo/data-structure-basic/graph-traverse-basic/)，以及经典的 [BFS 暴力穷举算法框架](https://labuladong.online/zh/algo/essential-technique/bfs-framework/)，所以这里要拓展延伸一下。

**回顾写法二，我们每向下遍历一层，就给 `depth` 加 1，可以理解为每条树枝的权重是 1，二叉树中每个节点的深度，其实就是从根节点到这个节点的路径权重和，且同一层的所有节点，路径权重和都是相同的**。

那么假设，如果每条树枝的权重可以是任意值，现在让你层序遍历整棵树，打印每个节点的路径权重和，你会怎么做？

这样的话，同一层节点的路径权重和就不一定相同了，写法二这样只维护一个 `depth` 变量就无法满足需求了。

写法三就是为了解决这个问题，在写法一的基础上添加一个 `State` 类，让每个节点自己负责维护自己的路径权重和，代码如下

```java
class State {
    TreeNode node; // 乘客（节点）
    int depth;	 // 票上的座位排数（深度）

    State(TreeNode node, int depth) {
        this.node = node;
        this.depth = depth;
    }
}

void levelOrderTraverse(TreeNode root) {
    if (root == null) {
        return;
    }
    Queue<State> q = new LinkedList<>();
    // 根节点的路径权重和是 1
    q.offer(new State(root, 1));

    while (!q.isEmpty()) {
        State cur = q.poll();
        // 访问 cur 节点，同时知道它的路径权重和
        System.out.println("depth = " + cur.depth + ", val = " + cur.node.val);

        // 把 cur 的左右子节点加入队列
        if (cur.node.left != null) {
            q.offer(new State(cur.node.left, cur.depth + 1));
        }
        if (cur.node.right != null) {
            q.offer(new State(cur.node.right, cur.depth + 1));
        }
    }
}
```

它通过引入一个辅助类 `State`，把“节点”和它“所在的深度（`depth`）”**绑定封装在了一起**。

**入队时写好深度：**

- 根节点入队时，深度写 `1`。
- 任何一个节点 `cur` 出队时，它的左右孩子入队，深度直接写死为 `cur.depth + 1`。

**不需要双重循环了：** 因为每个从队列里弹出来的 `cur`（`State` 对象）都自己携带着 `depth` 信息，我们不需要用 `for` 循环去帮它数层数了，直接读取 `cur.depth` 即可。



`new State(...)` 整体的意思是：**调用 `State` 类的构造函数，在内存中现场制造一个新的 `State` 对象。**

```java
// 第一步：new 出一个新的状态对象，用临时变量 s 指向它
State s = new State(cur.node.left, cur.depth + 1);

// 第二步：把这个新创建的对象 s 塞进队列里排队
q.offer(s);
```







## 三种层序遍历写法的对比

到目前为止，你已经看完了层序遍历的三种经典写法，它们各有胜场：

| **写法版本**               | **核心机制**                 | **优点**                                                   | **缺点 / 适用场景**                                          |
| -------------------------- | ---------------------------- | ---------------------------------------------------------- | ------------------------------------------------------------ |
| **第一版（基础款）**       | 单层 `while` + 队列          | 代码最简单，开销最小。                                     | **无法得知当前节点属于第几层**。只适用于单纯想把节点打印出来的场景。 |
| **第二版（Batch 批量款）** | `while` + `q.size()` + `for` | 不需要创建额外对象，内存友好，**格式极其整齐**。           | 适合**整层整层处理**的场景。比如 LeetCode 102（按层输出二维列表）。 |
| **第三版（State 绑定款）** | 辅助类 `State` 封装          | **极其灵活**。每个节点携带自己的“状态”，可以随意扩展属性。 | 每次入队都要 `new` 一个 `State` 对象，在节点极多时会带来额外的内存开销。 |



---





# 二叉树算法解题

|                           LeetCode                           |                             力扣                             | 难度 |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :--: |
| [104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/) | [104. 二叉树的最大深度](https://leetcode.cn/problems/maximum-depth-of-binary-tree/) |      |
| [144. Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/) | [144. 二叉树的前序遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal/) |      |
| [543. Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/) | [543. 二叉树的直径](https://leetcode.cn/problems/diameter-of-binary-tree/) |      |