|                           LeetCode                           |                             力扣                             | 难度 |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :--: |
| [773. Sliding Puzzle](https://leetcode.com/problems/sliding-puzzle/) | [773. 滑动谜题](https://leetcode.cn/problems/sliding-puzzle/) |      |
| [752. Open the Lock](https://leetcode.com/problems/open-the-lock/) | [752. 打开转盘锁](https://leetcode.cn/problems/open-the-lock/) |      |

DFS/回溯/BFS 这类算法，本质上就是把具体的问题抽象成树结构，然后遍历这棵树进行暴力穷举，所以这些穷举算法的代码本质上就是树的遍历代码

DFS/回溯算法的本质就是递归遍历一棵穷举树（多叉树），而多叉树的递归遍历又是从二叉树的递归遍历衍生出来的。所以我说 DFS/回溯算法的本质是二叉树的递归遍历。

BFS 算法的本质就是遍历一幅图，下面你就会看到了，BFS 的算法框架就是 [图结构的 DFS/BFS 遍历](https://labuladong.online/zh/algo/data-structure-basic/graph-traverse-basic/) 中遍历图节点的算法代码。

而图的遍历算法其实就是多叉树的遍历算法加了个 `visited` 数组防止死循环；多叉树的遍历算法又是从二叉树遍历算法衍生出来的。所以我说 BFS 算法的本质就是二叉树的层序遍历。



BFS 

- 求最短路径
- **所以阅读本文前，需要确保你学过前面的 [二叉树的递归/层序遍历](https://labuladong.online/zh/algo/data-structure-basic/binary-tree-traverse-basic/)、[多叉树的递归/层序遍历](https://labuladong.online/zh/algo/data-structure-basic/n-ary-tree-traverse-basic/) 和 [图结构的 DFS/BFS 遍历](https://labuladong.online/zh/algo/data-structure-basic/graph-traverse-basic/)，先把这几种基本数据结构的遍历算法玩明白，其他的算法都会很容易理解**。



### 算法框架

BFS 的算法框架其实就是 [图结构的 DFS/BFS 遍历](https://labuladong.online/zh/algo/data-structure-basic/graph-traverse-basic/) 中给出的 BFS 遍历图结构的代码，共有三种写法。

对于实际的 BFS 算法问题

- 第一种写法最简单，但局限性太大，不常用；
- 第二种写法最常用，中等难度的 BFS 算法题基本都可以用这种写法解决；
- 第三种写法稍微复杂一点，但灵活性最高，可能会在一些难度较大的的 BFS 问题中用到。在下一章的 [BFS 算法习题章节](https://labuladong.online/zh/algo/problem-set/bfs/) 中，会有一些难度更大的题目使用第三种写法，到时候你可以自己尝试。

本文的例题都是中等难度，所以本文给出的解法都以第二种写法为准：

```java
// 从 s 开始 BFS 遍历图的所有节点，且记录遍历的步数
// 当走到目标节点 target 时，返回步数
int bfs(int s, int target) {
    boolean[] visited = new boolean[graph.size()];
    Queue<Integer> q = new LinkedList<>();
    q.offer(s);
    visited[s] = true;
    // 记录从 s 开始走到当前节点的步数
    int step = 0;
    while (!q.isEmpty()) {
        int sz = q.size();
        for (int i = 0; i < sz; i++) {
            int cur = q.poll();
            System.out.println("visit " + cur + " at step " + step);
            // 判断是否到达终点
            if (cur == target) {
                return step;
            }
            // 将邻居节点加入队列，向四周扩散搜索
            for (int to : neighborsOf(cur)) {
                if (!visited[to]) {
                    q.offer(to);
                    visited[to] = true;
                }
            }
        }
        step++;
    }
    // 如果走到这里，说明在图中没有找到目标节点
    return -1;
}
```

上面这个代码框架几乎就是从 [图结构的 DFS/BFS 遍历](https://labuladong.online/zh/algo/data-structure-basic/graph-traverse-basic/) 中复制过来的，只不过添加了一个 `target` 参数，当第一次走到 `target` 时，直接结束算法并返回走过的步数。



### 滑动谜题

力扣第 773 题「[滑动谜题](https://leetcode.cn/problems/sliding-puzzle/)」就是一个可以运用 BFS 框架解决的题目

<img src="https://labuladong.online/images/algo/sliding_puzzle/3.jpeg" alt="img" style="zoom:50%;" />

以此类推，这四个邻居节点还有各自的四个邻居节点，那这不就是一幅图结构吗？

那么我从起点开始使用 BFS 算法遍历这幅图，第一次到达终点时，走过的步数就是答案。

```java
int bfs(int[][] board, int[][] target) {
    Queue<int[][]> q = new LinkedList<>();
    HashSet visited = new HashSet<>();

    // 将起点加入队列
    q.offer(board);
    visited.add(board);

    int step = 0;
    while (!q.isEmpty()) {
        int sz = q.size();
        for (int i = 0; i < sz; i++) {
            int[][] cur = q.poll();
            // 判断是否到达终点
            if (cur == target) {
                return step;
            }
            // 将当前节点的邻居节点加入队列
            for (int[][] neighbor : getNeighbors(cur)) {
                if (!visited.contains(neighbor)) {
                    q.offer(neighbor);
                    visited.add(neighbor);
                }
            }
        }
        step++;
    }
    return -1;
}

List<int[][]> getNeighbors(int[][] board) {
    // 将 board 中的数字 0 和上下左右的数字进行交换，得到 4 个邻居节点
}
```

对于这道题，我们抽象出来的图结构也是会包含环的，所以需要一个 `visited` 数组记录已经走过的节点，避免成环导致死循环。

还有一个问题，这道题中 `board` 是一个二维数组，我们在 [哈希表/哈希集合原理](https://labuladong.online/zh/algo/data-structure-basic/hashmap-basic/) 中介绍过，二维数组这种可变数据结构是无法直接加入哈希集合的。

所以我们还要再用一点技巧，想办法把二维数组转化成一个不可变类型才能存到哈希集合中。常见的解决方案是把二维数组序列化成一个字符串，这样就可以直接存入哈希集合了。

**其中比较有技巧性的点在于，二维数组有「上下左右」的概念，压缩成一维的字符串后后，还怎么把数字 0 和上下左右的数字进行交换**？

对于这道题，题目说输入的数组大小都是 2 x 3，所以我们可以直接手动写出来这个映射：

```java
// 记录一维字符串的相邻索引
int[][] neighbor = new int[][]{
    {1, 3},
    {0, 4, 2},
    {1, 5},
    {0, 4},
    {3, 1, 5},
    {4, 2}
};
```

**这个映射的含义就是，在一维字符串中，索引 `i` 在二维数组中的的相邻索引为 `neighbor[i]`**。

例如，我们可以知道 `neighbor[4]` 的周围元素为 `neighbor[3], neighbor[1], neighbor[5]`：

<img src="https://labuladong.online/images/algo/sliding_puzzle/4.jpeg" alt="img" style="zoom:50%;" />

这样，无论数字 0 在哪里，都可以通过这个索引映射得到它的相邻索引进行交换了。下面是完整的代码实现：

#### 答案

```java
class Solution {
    public int slidingPuzzle(int[][] board) {
        int m = 2, n = 3;
        StringBuilder sb = new StringBuilder();
        String target = "123450";
        // 将 2x3 的数组转化成字符串作为 BFS 的起点
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                sb.append(board[i][j]);
            }
        }
        String start = sb.toString();

        // 记录一维字符串的相邻索引
        int[][] neighbor = new int[][]{
                {1, 3},
                {0, 4, 2},
                {1, 5},
                {0, 4},
                {3, 1, 5},
                {4, 2}
        };

        // ****** BFS 算法框架开始 ******
        Queue<String> q = new LinkedList<>();
        HashSet<String> visited = new HashSet<>();
        // 从起点开始 BFS 搜索
        q.offer(start);
        visited.add(start);

        int step = 0;
        while (!q.isEmpty()) {
            int sz = q.size();
            for (int i = 0; i < sz; i++) {
                String cur = q.poll();
                // 判断是否达到目标局面
                if (target.equals(cur)) {
                    return step;
                }
                // 找到数字 0 的索引
                int idx = 0;
                for (; cur.charAt(idx) != '0'; idx++) ;
                // 将数字 0 和相邻的数字交换位置
                for (int adj : neighbor[idx]) {
                    String new_board = swap(cur.toCharArray(), adj, idx);
                    // 防止走回头路
                    if (!visited.contains(new_board)) {
                        q.offer(new_board);
                        visited.add(new_board);
                    }
                }
            }
            step++;
        }
        // ****** BFS 算法框架结束 ******
        return -1;
    }

    private String swap(char[] chars, int i, int j) {
        char temp = chars[i];
        chars[i] = chars[j];
        chars[j] = temp;
        return new String(chars);
    }

}
```



### 解开密码锁的最少次数

来看力扣第 752 题「[打开转盘锁](https://leetcode.cn/problems/open-the-lock/)」，

**第一步，我们不管所有的限制条件，不管 `deadends` 和 `target` 的限制，就思考一个问题：如果让你设计一个算法，穷举所有可能的密码组合，你怎么做**？

就从 `"0000"` 开始，如果你只转一下锁，有几种可能？总共有 4 个位置，每个位置可以向上转，也可以向下转，也就是可以穷举出 `"1000", "9000", "0100", "0900"...` 共 8 种密码。

然后，再以这 8 种密码作为基础，其中每个密码又可以转动一下衍生出 8 种密码，以此类推...

心里那棵递归树出来没有？应该是一棵八叉树，每个节点都有 8 个子节点，向下衍生。

下面这段伪码就描述了上述思路，用层序遍历一棵八叉树：

```java
// 将 s[j] 向上拨动一次
String plusOne(String s, int j) {
    char[] ch = s.toCharArray();
    if (ch[j] == '9')
        ch[j] = '0';
    else
        ch[j] += 1;
    return new String(ch);
}
// 将 s[i] 向下拨动一次
String minusOne(String s, int j) {
    char[] ch = s.toCharArray();
    if (ch[j] == '0')
        ch[j] = '9';
    else
        ch[j] -= 1;
    return new String(ch);
}

// BFS 框架，寻找最少的拨动次数
void BFS(String target) {
    Queue<String> q = new LinkedList<>();
    q.offer("0000");

    int step = 0;

    while (!q.isEmpty()) {
        int sz = q.size();
        // 将当前队列中的所有节点向周围扩散
        for (int i = 0; i < sz; i++) {
            String cur = q.poll();
            // 判断是否到达终点
            if (cur.equals(target)) {
                return step;
            }

            // 一个密码可以衍生出 8 种相邻的密码
            for (String neighbor : getNeighbors(cur)) {
                q.offer(neighbor);
            }
        }
        // 在这里增加步数
        step++;
    }
}
// 将 s 的每一位向上拨动一次或向下拨动一次，8 种相邻密码
List<String> getNeighbors(String s) {
    List<String> neighbors = new ArrayList<>();
    for (int i = 0; i < 4; i++) {
        neighbors.add(plusOne(s, i));
        neighbors.add(minusOne(s, i));
    }
    return neighbors;
}
```

这个代码已经可以穷举所有可能的密码组合了，但是还有些问题需要解决。

1、会走回头路，我们可以从 `"0000"` 拨到 `"1000"`，但是等从队列拿出 `"1000"` 时，还会拨出一个 `"0000"`，这样的话会产生死循环。

这个问题很好解决，其实就是成环了嘛，我们用一个 `visited` 集合记录已经穷举过的密码，再次遇到时，不要再加到队列里就行了。

2、没有对 `deadends` 进行处理，按道理这些「死亡密码」是不能出现的。

这个问题也好处理，额外用一个 `deadends` 集合记录这些死亡密码，凡是遇到这些密码，不要加到队列里就行了。

或者还可以更简单一些，直接把 `deadends` 中的死亡密码作为 `visited` 集合的初始元素，这样也可以达到目的。



#### 答案

```java
class Solution {
    public int openLock(String[] deadends, String target) {
        // 记录需要跳过的死亡密码
        Set<String> deads = new HashSet<>();
        for (String s : deadends) deads.add(s);
        if (deads.contains("0000")) return -1;

        // 记录已经穷举过的密码，防止走回头路
        Set<String> visited = new HashSet<>();
        Queue<String> q = new LinkedList<>();
        // 从起点开始启动广度优先搜索
        int step = 0;
        q.offer("0000");
        visited.add("0000");
        
        while (!q.isEmpty()) {
            int sz = q.size();
            // 将当前队列中的所有节点向周围扩散
            for (int i = 0; i < sz; i++) {
                String cur = q.poll();
                
                // 判断是否到达终点
                if (cur.equals(target))
                    return step;
                
                // 将一个节点的合法相邻节点加入队列
                for (String neighbor : getNeighbors(cur)) {
                    if (!visited.contains(neighbor) && !deads.contains(neighbor)) {
                        q.offer(neighbor);
                        visited.add(neighbor);
                    }
                }
            }
            // 在这里增加步数
            step++;
        }
        // 如果穷举完都没找到目标密码，那就是找不到了
        return -1;
    }

    // 将 s[j] 向上拨动一次
    String plusOne(String s, int j) {
        char[] ch = s.toCharArray();
        if (ch[j] == '9')
            ch[j] = '0';
        else
            ch[j] += 1;
        return new String(ch);
    }

    // 将 s[i] 向下拨动一次
    String minusOne(String s, int j) {
        char[] ch = s.toCharArray();
        if (ch[j] == '0')
            ch[j] = '9';
        else
            ch[j] -= 1;
        return new String(ch);
    }

    // 将 s 的每一位向上拨动一次或向下拨动一次，8 种相邻密码
    List<String> getNeighbors(String s) {
        List<String> neighbors = new ArrayList<>();
        for (int i = 0; i < 4; i++) {
            neighbors.add(plusOne(s, i));
            neighbors.add(minusOne(s, i));
        }
        return neighbors;
    }
}
```



### 双向BFS优化

下面再介绍一种 BFS 算法的优化思路：**双向 BFS**，可以提高 BFS 搜索的效率。

你把这种技巧当做扩展阅读就行，在一般的面试笔试题中，普通的 BFS 算法已经够用了，如果遇到超时无法通过，或者面试官的追问，可以考虑解法是否需要双向 BFS 优化。

双向 BFS 就是从标准的 BFS 算法衍生出来的：

**传统的 BFS 框架是从起点开始向四周扩散，遇到终点时停止；而双向 BFS 则是从起点和终点同时开始扩散，当两边有交集的时候停止**。

为什么这样能够能够提升效率呢？

就好比有 A 和 B 两个人，传统 BFS 就相当于 A 出发去找 B，而 B 待在原地不动；双向 BFS 则是 A 和 B 一起出发，双向奔赴。那当然第二种情况下 A 和 B 可以更快相遇。

<img src="https://labuladong.online/images/algo/bfs/1.jpeg" alt="img" style="zoom:50%;" />

<img src="https://labuladong.online/images/algo/bfs/2.jpeg" alt="img" style="zoom:50%;" />

图示中的树形结构，如果终点在最底部，按照传统 BFS 算法的策略，会把整棵树的节点都搜索一遍，最后找到 `target`；而双向 BFS 其实只遍历了半棵树就出现了交集，也就是找到了最短距离。

当然从 Big O 表示法分析算法复杂度的话，这两种 BFS 在最坏情况下都可能遍历完所有节点，所以理论时间复杂度都是 O(N)*O*(*N*)，但实际运行中双向 BFS 确实会更快一些。



```markdown
双向 BFS 的局限性

**你必须知道终点在哪里，才能使用双向 BFS 进行优化**。

对于 BFS 算法，我们肯定是知道起点的，但是终点具体是什么，我们在一开始可能并不知道。

比如上面的密码锁问题和滑动拼图问题，题目都明确给出了终点，都可以用双向 BFS 进行优化。

但比如我们在 [二叉树的 DFS/BFS 遍历](https://labuladong.online/zh/algo/data-structure-basic/binary-tree-traverse-basic/) 中讨论的二叉树最小高度的问题，起点是根节点，终点是距离根节点最近的叶子节点，你在算法开始时并不知道终点具体在哪里，所以就没办法使用双向 BFS 进行优化。
```



下面我们就以密码锁问题为例，看看如何将普通 BFS 算法优化为双向 BFS 算法，直接看代码吧：

```java
class Solution {
    public int openLock(String[] deadends, String target) {
        Set<String> deads = new HashSet<>();
        for (String s : deadends) deads.add(s);
        // base case
        if (deads.contains("0000")) return -1;
        if (target.equals("0000")) return 0;

        // 用集合不用队列，可以快速判断元素是否存在
        Set<String> q1 = new HashSet<>();
        Set<String> q2 = new HashSet<>();
        Set<String> visited = new HashSet<>();
        
        int step = 0;
        q1.add("0000");
        visited.add("0000");
        q2.add(target);
        visited.add(target);

        while (!q1.isEmpty() && !q2.isEmpty()) {
            // 在这里增加步数
            step++;

            // 哈希集合在遍历的过程中不能修改，所以用 newQ1 存储邻居节点
            Set<String> newQ1 = new HashSet<>();

            // 获取 q1 中的所有节点的邻居
            for (String cur : q1) {
                // 将一个节点的未遍历相邻节点加入集合
                for (String neighbor : getNeighbors(cur)) {
                    // 判断是否到达终点
                    if (q2.contains(neighbor)) {
                        return step;
                    }
                    if (!visited.contains(neighbor) && !deads.contains(neighbor)) {
                        newQ1.add(neighbor);
                        visited.add(neighbor);
                    }
                }
            }
            // newQ1 存储着 q1 的邻居节点
            q1 = newQ1;
            // 因为每次 BFS 都是扩散 q1，所以把元素数量少的集合作为 q1
            if (q1.size() > q2.size()) {
                Set<String> temp = q1;
                q1 = q2;
                q2 = temp;
            }
        }
        return -1;
    }

    // 将 s[j] 向上拨动一次
    String plusOne(String s, int j) {
        char[] ch = s.toCharArray();
        if (ch[j] == '9')
            ch[j] = '0';
        else
            ch[j] += 1;
        return new String(ch);
    }

    // 将 s[i] 向下拨动一次
    String minusOne(String s, int j) {
        char[] ch = s.toCharArray();
        if (ch[j] == '0')
            ch[j] = '9';
        else
            ch[j] -= 1;
        return new String(ch);
    }

    List<String> getNeighbors(String s) {
        List<String> neighbors = new ArrayList<>();
        for (int i = 0; i < 4; i++) {
            neighbors.add(plusOne(s, i));
            neighbors.add(minusOne(s, i));
        }
        return neighbors;
    }
}
```

双向 BFS 还是遵循 BFS 算法框架的，但是有几个细节区别：

1、不再使用队列存储元素，而是改用 [哈希集合](https://labuladong.online/zh/algo/data-structure-basic/hash-set/)，方便快速判两个集合是否有交集。

2、调整了 return step 的位置。因为双向 BFS 中不再是简单地判断是否到达终点，而是判断两个集合是否有交集，所以要在计算出邻居节点时就进行判断。

3、还有一个优化点，每次都保持 `q1` 是元素数量较小的集合，这样可以一定程度减少搜索次数。

因为按照 BFS 的逻辑，队列（集合）中的元素越多，扩散邻居节点之后新的队列（集合）中的元素就越多；在双向 BFS 算法中，如果我们每次都选择一个较小的集合进行扩散，那么占用的空间增长速度就会慢一些，效率就会高一些。

不过话说回来，**无论传统 BFS 还是双向 BFS，无论做不做优化，从 Big O 衡量标准来看，时间复杂度都是一样的**，只能说双向 BFS 是一种进阶技巧，算法运行的速度会相对快一点，掌握不掌握其实都无所谓。

最关键的还是要把 BFS 通用框架记下来，并且做到熟练运用，后面有 [BFS 习题章节](https://labuladong.online/zh/algo/problem-set/bfs/)，请你尝试运用本文的技巧，解决其中的题目。