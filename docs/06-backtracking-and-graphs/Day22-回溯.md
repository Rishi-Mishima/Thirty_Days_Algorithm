|                           LeetCode                           |                           力扣                           | 难度 |
| :----------------------------------------------------------: | :------------------------------------------------------: | :--: |
| [46. Permutations](https://leetcode.com/problems/permutations/) | [46. 全排列](https://leetcode.cn/problems/permutations/) |  🟡   |

阅读本文前，你需要先学习：

- [二叉树结构基础](https://labuladong.online/zh/algo/data-structure-basic/binary-tree-basic/)
- [二叉树的遍历框架](https://labuladong.online/zh/algo/data-structure-basic/binary-tree-traverse-basic/)
- [多叉树结构及遍历框架](https://labuladong.online/zh/algo/data-structure-basic/n-ary-tree-traverse-basic/)



### 回溯算法

回溯算法和我们常说的 DFS 算法基本可以认为是同一种算法

> **抽象地说，解决一个回溯问题，实际上就是遍历一棵决策树的过程，树的每个叶子节点存放着一个合法答案。你把整棵树遍历一遍，把叶子节点上的答案都收集起来，就能得到所有的合法答案**。

站在回溯树的一个节点上，你只需要思考 3 个问题：

1、路径：也就是已经做出的选择。

2、选择列表：也就是你当前可以做的选择。

3、结束条件：也就是到达决策树底层，无法再做选择的条件。



#### 回溯算法框架

```python 
result = []
def backtrack(路径, 选择列表):
    if 满足结束条件:
        result.add(路径)
        return
    
    for 选择 in 选择列表:
        做选择
        backtrack(路径, 选择列表)
        撤销选择
```



### 全排列

##### 力扣46. 全排列(HOT100)

**我们这次讨论的全排列问题不包含重复的数字，包含重复数字的扩展场景我在后文 [回溯算法秒杀排列组合子集的九种题型](https://labuladong.online/zh/algo/essential-technique/permutation-combination-subset-all-in-one/) 中讲解**。

另外，有些读者之前看过的全排列算法代码可能是那种 `swap` 交换元素的写法，和我在本文介绍的代码不同。这是回溯算法两种穷举思路，我会在后文 [球盒模型：回溯算法穷举的两种视角](https://labuladong.online/zh/algo/practice-in-action/two-views-of-backtrack/) 讲明白。现在还不适合直接跟你讲那个解法，你照着我的思路学习即可。



穷举排列组合[1, 2, 3]

<img src="https://labuladong.online/images/algo/backtracking/1.jpg" alt="img" style="zoom:33%;" />

只要从根遍历这棵树，记录路径上的数字，其实就是所有的全排列。**我们不妨把这棵树称为回溯算法的「决策树」**。

**为啥说这是决策树呢，因为你在每个节点上其实都在做决策**。比如说你站在下图的红色节点上：

<img src="https://labuladong.online/images/algo/backtracking/2.jpg" alt="img" style="zoom:33%;" />

你现在就在做决策，可以选择 1 那条树枝，也可以选择 3 那条树枝。为啥只能在 1 和 3 之中选择呢？因为 2 这个树枝在你身后，这个选择你之前做过了，而全排列是不允许重复使用数字的。

**现在可以解答开头的几个名词：**

- **`[2]` 就是「路径」，记录你已经做过的选择；**
- **`[1,3]` 就是「选择列表」，表示你当前可以做出的选择；**
- **「结束条件」就是遍历到树的底层叶子节点，这里也就是选择列表为空的时候**。

如果明白了这几个名词，可以把「路径」和「选择」列表作为决策树上每个节点的属性，比如下图列出了几个蓝色节点的属性：

<img src="https://labuladong.online/images/algo/backtracking/3.jpg" alt="img" style="zoom:33%;" />

**我们定义的 `backtrack` 函数其实就像一个指针，在这棵树上游走，同时要正确维护每个节点的属性，每当走到树的底层叶子节点，其「路径」就是一个全排列**。

再进一步，如何遍历一棵树？这个应该不难吧。回忆一下之前 [学习数据结构的框架思维](https://labuladong.online/zh/algo/essential-technique/algorithm-summary/) 写过，各种搜索问题其实都是树的遍历问题，而多叉树的遍历框架就是这样：

```java
void traverse(TreeNode root) {
    for (TreeNode child : root.childern) {
        // 前序位置需要的操作
        traverse(child);
        // 后序位置需要的操作
    }
}
```

细心的读者肯定会疑问：多叉树 DFS 遍历框架的前序位置和后序位置应该在 for 循环外面，并不应该是在 for 循环里面呀？为什么在回溯算法中跑到 for 循环里面了？

是的，DFS 算法的前序和后序位置应该在 for 循环外面，不过回溯算法和 DFS 算法略有不同，[解答回溯/DFS 算法的若干疑问](https://labuladong.online/zh/algo/essential-technique/backtrack-vs-dfs/) 会具体讲解，这里可以暂且忽略这个问题。

**前序遍历的代码在进入某一个节点之前的那个时间点执行，后序遍历代码在离开某个节点之后的那个时间点执行**。

<img src="https://labuladong.online/images/algo/backtracking/5.jpg" alt="img" style="zoom:33%;" />

**我们只要在递归之前做出选择，在递归之后撤销刚才的选择**，就能正确得到每个节点的选择列表和路径。



#### 完整模板

```java
class Solution {

    List<List<Integer>> res = new LinkedList<>();

    // 主函数，输入一组不重复的数字，返回它们的全排列
    List<List<Integer>> permute(int[] nums) {
        // 记录「路径」
        LinkedList<Integer> track = new LinkedList<>();
        // 「路径」中的元素会被标记为 true，避免重复使用
        boolean[] used = new boolean[nums.length];
        
        backtrack(nums, track, used);
        return res;
    }

    // 路径：记录在 track 中
    // 选择列表：nums 中不存在于 track 的那些元素（used[i] 为 false）
    // 结束条件：nums 中的元素全都在 track 中出现
    void backtrack(int[] nums, LinkedList<Integer> track, boolean[] used) {
        // 触发结束条件
        if (track.size() == nums.length) {
            res.add(new LinkedList(track));
            return;
        }

        for (int i = 0; i < nums.length; i++) {
            // 排除不合法的选择
            if (used[i]) {
                // nums[i] 已经在 track 中，跳过
                continue;
            }
            // 做选择
            track.add(nums[i]);
            used[i] = true;
            // 进入下一层决策树
            backtrack(nums, track, used);
            // 取消选择
            track.removeLast();
            used[i] = false;
        }
    }
}
```

我们这里稍微做了些变通，没有显式记录「选择列表」，而是通过 `used` 数组排除已经存在 `track` 中的元素，从而推导出当前的选择列表

<img src="https://labuladong.online/images/algo/backtracking/6.jpg" alt="img" style="zoom:33%;" />

但是必须说明的是，不管怎么优化，都符合回溯框架，而且时间复杂度都不可能低于 O(N!)，因为穷举整棵决策树是无法避免的，你最后肯定要穷举出 N! 种全排列结果。

**这也是回溯算法的一个特点，不像动态规划存在重叠子问题可以优化，回溯算法就是纯暴力穷举，复杂度一般都很高**。



回溯算法就是个多叉树的遍历问题，关键就是在前序遍历和后序遍历的位置做一些操作，算法框架如下：

```python 
def backtrack(...):
    for 选择 in 选择列表:
        做选择
        backtrack(...)
        撤销选择
```

**写 `backtrack` 函数时，需要维护走过的「路径」和当前可以做的「选择列表」，当触发「结束条件」时，将「路径」记入结果集**。





#### 力扣46. 全排列

力扣 46 题《全排列》（Permutations）是回溯算法（Backtracking）领域最经典的入门必刷题。

它的核心考点是：**穷举所有可能性**。我们可以把解题过程想象成填N 个位置的盒子，每个数字只能用一次。

回溯算法本质上就是**深度优先搜索（DFS）**，包含三个核心步骤：

1. **选择**：在当前位置放入一个还没用过的数字。
2. **递归**：去填下一个位置。
3. **撤销选择（回溯）**：把刚才放入的数字拿出来，试着放下一个数字。

为了防止同一个数字被重复使用，我们需要一个 `visited` 数组（或者 `boolean[]`）来记录哪些数字已经被放进当前路径中了。