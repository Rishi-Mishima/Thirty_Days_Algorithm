深度优先搜索（DFS）和回溯算法（Backtracking）就像是一对**孪生兄弟**

简单来说：**DFS 是一种“搜索策略”，而回溯是 DFS 树上的一种“状态恢复动作”。**

| **维度**     | **深度优先搜索 (DFS)**                                     | **回溯算法 (Backtracking)**                                  |
| ------------ | ---------------------------------------------------------- | ------------------------------------------------------------ |
| **核心思想** | **不撞南墙不回头**。沿着一条路径一直往下搜，搜到底再退回。 | **试错 + 撤销**。做选择  -> 深入递归 ->发现不行/已到头 -> **撤销选择** ->换条路再试。 |
| **典型目标** | 遍历整张图/树、寻找连通块、找**是否存在**某条路径。        | 穷举所有可能的**解集**（如所有组合、排列、子集、棋盘摆法）。 |
| **关键动作** | 访问节点、标记已访问。                                     | **显式的“撤销选择”**（如 `path.pop()`, `used[i] = false`）。 |

**纯 DFS：** 走过的路（节点）打了标记就不需要取消了（例如求网格中的岛屿数量，变成海水就不用变回来了）。

**回溯：** 走过的路退回来时**必须把标记恢复原状**，因为后面的其他分支还要继续使用这个节点（例如上一题的全排列）。

### 回溯算法的万能模板

```java
void backtrack(路径 path, 选择列表 choices) {
    // 1. 终止条件：满足收集结果的要求
    if (满足到达叶子节点/目标状态) {
        res.add(new ArrayList<>(path)); // 注意深拷贝！
        return;
    }

    // 2. 遍历所有可选项
    for (选择 choice : choices) {
        // 剪枝：排除不合法的选择
        if (不合法) continue;

        // --- 做选择 ---
        path.add(choice);
        标记 choice 已使用;

        // --- 递归：进入下一层决策树 ---
        backtrack(path, 剩余 choices);

        // --- 撤销选择 (回溯的核心) ---
        path.removeLast();
        取消 choice 的使用标记;
    }
}
```



## 必刷力扣练习题清单（按类型梯度分类）

建议按照下面的分类顺序依次练习，体会“纯 DFS”与“回溯”的差异：

### 1. 纯 DFS 专题（网格/图遍历，不需要撤销状态）

这类题目的特点是：遍历过的单元格直接标记（如改成 `'0'` 或 `visited[r][c] = true`），退栈时**不需要**抹去标记。

- **LeetCode 200. 岛屿数量** `中等` （DFS 入门第一题，淹没连通块）
- **LeetCode 695. 岛屿的最大面积** `中等` （在 DFS 中计算并累加节点数）
- **LeetCode 130. 被围绕的区域** `中等` （从边界反向 DFS 的逆向思维）

### 2. 回溯三大基本型（组合、子集、排列）

这三道题是回溯算法的**绝对基石**，掌握了它们就掌握了 80% 的回溯思想：

- **LeetCode 78. 子集** `中等` （树枝收集结果，不需要特定终止条件）
- **LeetCode 77. 组合** `中等` （固定长度 $k$ 的组合，注意使用 `startIndex` 控制避免重复）
- **LeetCode 46. 全排列** `中等` （我们刚讲过的题目，使用 `used` 数组控制选择）

### 3. 回溯进阶（带重复元素的“剪枝”去重）

当输入的数组中有重复元素，且答案要求不能重复时，就需要在回溯中进行**树层剪枝**：

- **LeetCode 90. 子集 II** `中等` （包含重复元素的子集）
- **LeetCode 40. 组合总和 II** `中等` （数组元素只能用一次，树层去重）
- **LeetCode 47. 全排列 II** `中等` （带重复元素的全排列）

### 4. 棋盘与复杂约束问题（大厂常考硬核题）

这类问题空间很大，需要多重约束判断：

- **LeetCode 51. N 皇后** `困难` （经典的二维棋盘回溯，检查行、列、两条斜线约束）
- **LeetCode 79. 单词搜索** `中等` （网格上的回溯！因为同一条路径上字母不能复用，走过去要标记，**退回来时要把网格还原**）
- **LeetCode 37. 解数独** `困难` （二维回溯，每个格子尝试 1-9）

## 💡 刷题路线建议

1. 先做 **LeetCode 200 (岛屿数量)** 感受 **纯 DFS** 的遍历过程。
2. 再做 **LeetCode 78 (子集) $\rightarrow$ 77 (组合) $\rightarrow$ 46 (全排列)** 熟练掌握 **回溯万能模板**。
3. 最后做 **LeetCode 79 (单词搜索)**，体会 **“在网格图中做回溯（走不通恢复现场）”** 与 **“纯 DFS（直接标记访问）”** 的精妙区别！



它的典型目标是：**淹没一块区域、寻找一条可达路径、或者统计连通块的数量。**



## 纯 DFS 的万能模板（以网格/二维矩阵为例）

在面试中，90% 的纯 DFS 题目（比如岛屿系列）都可以直接套用下面这个“四方向网格 DFS 模板”：

```java
class Solution {
    // 1. 定义方向数组（上下左右），方便用循环走四个方向
    private int[][] dirs = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

    public void dfs(char[][] grid, int r, int c) {
        int m = grid.length;
        int n = grid[0].length;

        // 【边界检查 & Base Case】
        // 如果超出边界，或者碰到了障碍物/水/已经访问过的节点，直接返回
        if (r < 0 || r >= m || c < 0 || c >= n || grid[r][c] == '0') {
            return;
        }

        // 【标记已访问】
        // 关键点：纯 DFS 通常直接修改原数组（比如把陆地 '1' 变成水 '0'）
        // 这样就不需要专门的 visited 数组，也【不需要】像回溯那样改回来！
        grid[r][c] = '0';

        // 【向四个方向扩散搜索】
        for (int[] dir : dirs) {
            int nextR = r + dir[0];
            int nextC = c + dir[1];
            dfs(grid, nextR, nextC); // 递归深入
        }
    }
}
```



##  纯 DFS vs 回溯 的关键对比小结

为了让你在面试中一眼识别出该用哪个，记住这个对比表：

| **视角**     | **纯 DFS（如：岛屿数量）**       | **回溯（如：全排列、单词搜索）**                |
| ------------ | -------------------------------- | ----------------------------------------------- |
| **访问标记** | `grid[r][c] = '0'`               | `visited[r][c] = true`                          |
| **退栈动作** | **啥都不做**（染黑了就黑着）     | `visited[r][c] = false` （**必须恢复现场**）    |
| **主要目的** | 把这个连通区域“吃掉”或一次性走完 | 尝试走一步，不行就退回一步换别的方法            |
| **结果形式** | 得到一个最终统计值（如 `count`） | 收集**所有**可能路径的集合（`List<List<...>>`） |





### 两种算法框架对比

但是为什么有些时候会看到代码在 for 循环的前面「做选择」，在 for 循环的后面「撤销选择」呢：

**第一种写法是标准的回溯算法框架，第二种写法，如果非要区分的话，它其实应该归为 DFS 算法框架。**

```java
// 回溯算法框架模板
void backtrack(...) {
    if (reached the leaf node) {
        // 到达叶子节点，结束递归
        return;
    }

    for (int i = 0; i < n; i++;) { 
        // 做选择
        ...

        backtrack(...)

        // 撤销选择
        ...
    }
}

// DFS 算法框架模板
void dfs(...) {
    if (reached the leaf node) {
        // 到达叶子节点，结束递归
        return;
    }
    // 做选择
    ...
    for (int i = 0; i < n; i++) {
        dfs(...)
    }
    // 撤销选择
    ...
}
```



回溯算法 和 DFS

- **它俩的本质是一样的，都是「遍历」思维下的暴力穷举算法。唯一的区别在于关注点不同，回溯算法的关注点在「树枝」，DFS 算法的关注点在「节点」**。

  - 题目让你把多叉树上的每个节点都打印出来，那么你就用 DFS 算法框架

    - ```java
      void dfs(Node root) {
          if (root == null) {
              return;
          }
      
          print(root.val);
      
          for (Node child : root.children) {
              dfs(child);
          }
      }
      ```

  - 如果你非要把这个 print 代码写到 for 循环里面，那么最终打印出来的结果就会漏掉整棵树的根节点

    - ```java
      void backtrack(Node root) {
          if (root == null) {
              return;
          }
      
          for (Node child : root.children) {
              // 非要这样写的话，这棵树的根节点会被漏掉
              print(child.val);
              backtrack(child);
          }
      }
      ```



#### Traverse不需要返回值

我随口编一道简单的题目作为具体的例子：输入一棵二叉树根节点和一个 `targetVal`，请你在这棵树中找到任意一个值为 `targetVal` 的节点，如果不存在，返回 null。

```java
// 记录答案
TreeNode res = null;

TreeNode findTarget(TreeNode root, int targetVal) {
    traverse(root, targetVal);
    return res;
}

// 标准二叉树遍历框架
void traverse(TreeNode root, int targetVal) {
    if (root == null) {
        return;
    }
    if (res != null) {
        // 已经找到一个答案了，直接返回，终止递归
        return;
    }
    if (root.val == targetVal) {
        res = root;
        return;
    }

    // 标准遍历框架，遍历二叉树的所有结点
    traverse(root.left, targetVal);
    traverse(root.right, targetVal);
}
```

这段代码之所以**不需要返回值**（定义为 `void`），核心原因在于：**它采用了“全局变量/外部变量记录结果 + 遍历”的设计模式，而不是“分解问题（带返回值）”的递归模式。**



 答案被存在了全局变量 `res` 里

在 `traverse` 函数内部：

- 当它找到目标节点时，执行的是 `res = root;`（把答案直接写进了外部的 `res` 变量中）。
- 既然答案已经安全地存放在了 `res` 里，`traverse` 函数就**不需要**再通过 `return` 把节点一路层层向上传递回去了。
- 最终的 `findTarget` 函数直接读取 `res` 并返回即可。



这里的 `return;` 只是用来“控制流程”，而不是“传递数据”

代码里的 `return;` 出现了三次，它们的作用都是**单纯的“提前结束当前递归”**（也叫剪枝/终止条件）：

1. `if (root == null) return;` $\rightarrow$ 走到叶子节点外了，无事可做，退出。
2. `if (res != null) return;` $\rightarrow$ 别的分支已经找到答案了，后面没必要搜了，赶紧退出。
3. `res = root; return;` $\rightarrow$ 刚好找到答案，存下后退出。



##### 带返回值的写法

```java
// 带着返回值的写法（不需要全局变量 res）
TreeNode findTarget(TreeNode root, int targetVal) {
    // 1. base case：没找到返回 null
    if (root == null) {
        return null;
    }
    
    // 2. 找到目标，直接把当前节点作为【返回值】层层向上递交
    if (root.val == targetVal) {
        return root;
    }

    // 3. 去左子树找
    TreeNode left = findTarget(root.left, targetVal);
    if (left != null) {
        return left; // 左子树找到了，直接向上层返回，不再看右子树
    }

    // 4. 左子树没找到，去右子树找并返回结果
    return findTarget(root.right, targetVal);
}
```



---

# 🏝️岛屿题目合集

|                           LeetCode                           |                             力扣                             | 难度 |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :--: |
| [200. Number of Islands](https://leetcode.com/problems/number-of-islands/) | [200. 岛屿数量](https://leetcode.cn/problems/number-of-islands/) |  🟡   |
| [1254. Number of Closed Islands](https://leetcode.com/problems/number-of-closed-islands/) | [1254. 统计封闭岛屿的数目](https://leetcode.cn/problems/number-of-closed-islands/) |  🟡   |
| [1020. Number of Enclaves](https://leetcode.com/problems/number-of-enclaves/) | [1020. 飞地的数量](https://leetcode.cn/problems/number-of-enclaves/) |  🟡   |
| [695. Max Area of Island](https://leetcode.com/problems/max-area-of-island/) | [695. 岛屿的最大面积](https://leetcode.cn/problems/max-area-of-island/) |  🟡   |
| [1905. Count Sub Islands](https://leetcode.com/problems/count-sub-islands/) | [1905. 统计子岛屿](https://leetcode.cn/problems/count-sub-islands/) |  🟡   |
| [694. Number of Distinct Islands](https://leetcode.com/problems/number-of-distinct-islands/)🔒 | [694. 不同岛屿的数量](https://leetcode.cn/problems/number-of-distinct-islands/)🔒 |  🟡   |

> **岛屿系列题目的核心考点就是用 DFS/BFS 算法遍历二维数组**。

那么如何在二维矩阵中使用 DFS 搜索呢？

- 如果你把二维矩阵中的每一个位置看做一个节点，这个节点的上下左右四个位置就是相邻节点，那么整个矩阵就可以抽象成一幅网状的「图」结构。



### DFS 代码框架

```java
// 二叉树遍历框架
void traverse(TreeNode root) {
    traverse(root.left);
    traverse(root.right);
}

// 二维矩阵遍历框架
void dfs(int[][] grid, int i, int j, boolean[][] visited) {
    int m = grid.length, n = grid[0].length;
    if (i < 0 || j < 0 || i >= m || j >= n) {
        // 超出索引边界
        return;
    }
    if (visited[i][j]) {
        // 已遍历过 (i, j)
        return;
    }

    // 进入当前节点 (i, j)
    visited[i][j] = true;

    // 进入相邻节点（四叉树）
    // 上
    dfs(grid, i - 1, j, visited);
    // 下
    dfs(grid, i + 1, j, visited);
    // 左
    dfs(grid, i, j - 1, visited);
    // 右
    dfs(grid, i, j + 1, visited);
}
```

因为二维矩阵本质上是一幅「图」，所以遍历的过程中需要一个 `visited` 布尔数组防止走回头路，如果你能理解上面这段代码，那么搞定所有岛屿系列题目都很简单。

这里额外说一个处理二维数组的常用小技巧，你有时会看到使用「方向数组」来处理上下左右的遍历，和前文 [union-find 算法详解](https://labuladong.online/zh/algo/data-structure/union-find/) 的代码很类似：

```java
// 方向数组，分别代表上、下、左、右
int[][] dirs = new int[][]{{-1,0}, {1,0}, {0,-1}, {0,1}};

void dfs(int[][] grid, int i, int j, boolean[][] visited) {
    int m = grid.length, n = grid[0].length;
    if (i < 0 || j < 0 || i >= m || j >= n) {
        // 超出索引边界
        return;
    }
    if (visited[i][j]) {
        // 已遍历过 (i, j)
        return;
    }

    // 进入节点 (i, j)
    visited[i][j] = true;
    // 递归遍历上下左右的节点
    for (int[] d : dirs) {
        int next_i = i + d[0];
        int next_j = j + d[1];
        dfs(grid, next_i, next_j, visited);
    }
    // 离开节点 (i, j)
}
```



### 力扣200. 小岛的数量(HOT100)

**时间复杂度：O(M x N) **

- 虽然代码里既有双重循环又有递归，但因为每个格子最多只会被从 `'1'` 变成 `'0'` **一次**，之后再碰到它时立刻就会在 `if (grid[i][j] == '0') return;` 被拦截下来。因此整体运行效率极高。

**空间复杂度：O(M x N)**

- 最坏情况下（整个网格全是陆地 `'1'`），递归调用栈的最大深度可以达到 $M \times N$。



`grid.length` 为什么是行数 m 

- `grid[0].length` 为什么是列数 n 

<img src="/Users/ruixue/Library/Application Support/typora-user-images/image-20260722144248781.png" alt="image-20260722144248781" style="zoom:50%;" />

此题DFS的任务是

DFS 的任务是：

> 从当前位置出发，把所有与当前陆地相连的陆地找出来。

调用四次DFS

```java
     					(row - 1, col)
                     上

(row, col - 1) 左   当前   右 (row, col + 1)

              (row + 1, col)
                     下
                     
dfs(grid, row - 1, col); // 上
dfs(grid, row + 1, col); // 下
dfs(grid, row, col - 1); // 左
dfs(grid, row, col + 1); // 右
```

`count++` 统计的是 DFS 的启动次数，而每次 DFS 恰好处理一整座岛。



#### 答案

```java
class Solution {
    // 主函数，计算岛屿数量
    int numIslands(char[][] grid) {
        int res = 0;
        int m = grid.length, n = grid[0].length;
        // 遍历 grid
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == '1') {
                    // 每发现一个岛屿，岛屿数量加一
                    res++;
                    // 然后使用 DFS 将岛屿淹了
                    dfs(grid, i, j);
                }
            }
        }
        return res;
    }

    // 从 (i, j) 开始，将与之相邻的陆地都变成海水
    void dfs(char[][] grid, int i, int j) {
        int m = grid.length, n = grid[0].length;
        if (i < 0 || j < 0 || i >= m || j >= n) {
            // 超出索引边界
            return;
        }
        if (grid[i][j] == '0') {
            // 已经是海水了
            return;
        }
        // 将 (i, j) 变成海水
        grid[i][j] = '0';
        // 淹没上下左右的陆地
        dfs(grid, i + 1, j);
        dfs(grid, i, j + 1);
        dfs(grid, i - 1, j);
        dfs(grid, i, j - 1);
    }
}
```





### 力扣1254. 统计封闭岛屿的数量

