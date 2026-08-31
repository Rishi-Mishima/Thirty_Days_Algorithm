```markdown 
需要先熟练掌握多叉树的遍历, BFS, DFS
> 图结构就是多叉树结构的延伸

掌握邻接表和邻接矩阵实现图结构

了解不同种类的图结构实现
- 有向加权图
- 无向图
> 分别用邻接表邻接矩阵实现

图结构的DFS, BFS遍历
- 遍历所有的节点, 边, 路径

力扣797.所有可能的路径

BFS 三种写法
```



图结构就是 [多叉树结构](https://labuladong.online/zh/algo/data-structure-basic/n-ary-tree-traverse-basic/) 的延伸。图结构逻辑上由若干节点（`Vertex`）和边（`Edge`）构成，我们一般用邻接表、邻接矩阵等方式来存储图。

在树结构中，只允许父节点指向子节点，不存在子节点指向父节点的情况，子节点之间也不会互相链接；而图中没有那么多限制，节点之间可以相互指向，形成复杂的网络结构。

### 图的逻辑结构

<img src="https://labuladong.online/images/algo/graph/0.jpg" alt="img" style="zoom:50%;" />

一幅图是由**节点 (Vertex)** 和**边 (Edge)** 构成的

注意看我把「逻辑结构」和「具体实现」分开了。就好比前文 [二叉堆的原理和实现](https://labuladong.online/zh/algo/data-structure-basic/binary-heap-implement/) 一样，二叉堆的逻辑结构是一棵完全二叉树，但我们实际上用的数组来实现它。

根据图的逻辑结构，我们可以认为每个节点的实现如下：

```java
// 图节点的逻辑结构
class Vertex {
    int id;
    Vertex[] neighbors;
}
```

它和我们之前说的 [多叉树节点](https://labuladong.online/zh/algo/data-structure-basic/n-ary-tree-traverse-basic/) 几乎完全一样：

```java
// 基本的 N 叉树节点
class TreeNode {
    int val;
    TreeNode[] children;
}
```

所以说，图真的没啥高深的，本质上就是个高级点的多叉树而已，适用于树的 DFS/BFS 遍历算法，全部适用于图。

上面讲解的图结构是「逻辑上的」，具体实现上，我们很少用这个 `Vertex` 类，而是用**邻接表、邻接矩阵**来实现图结构。



### 邻接表和邻接矩阵实现图结构

邻接表和邻接矩阵是图结构的两种实现方法。

邻接表和邻接矩阵的存储方式分别如下：

<img src="https://labuladong.online/images/algo/graph/2.jpeg" alt="img" style="zoom:50%;" />

邻接表很直观，我把每个节点 `x` 的邻居都存到一个列表里，然后把 `x` 和这个列表映射起来，这样就可以通过一个节点 `x` 找到它的所有相邻节点。

邻接矩阵则是一个二维布尔数组，我们权且称为 `matrix`，如果节点 `x` 和 `y` 是相连的，那么就把 `matrix[x][y]` 设为 `true`（上图中绿色的方格代表 `true`）。如果想找节点 `x` 的邻居，去扫一圈 `matrix[x][..]` 就行了。

如果用代码的形式来表现，邻接表和邻接矩阵大概长这样：

```java
// 邻接表
// graph[x] 存储 x 的所有邻居节点
List<Integer>[] graph;


// 邻接矩阵
// matrix[x][y] 记录 x 是否有一条指向 y 的边
boolean[][] matrix;
```



> > 节点类型不是 int 怎么办
>
> 上述讲解中，我默认图节点是一个从 0 开始的整数，所以才能存储到邻接表和邻接矩阵中，通过索引访问。
>
> 但实际问题中，图节点可能是其他类型，比如字符串、自定义类等，那应该怎么存储呢？
>
> 很简单，你再额外使用一个哈希表，把实际节点和整数 id 映射起来，然后就可以用邻接表和邻接矩阵存储整数 id 了。
>
> **后面的讲解及习题中，我都会默认图节点是整数 id**。

那么，为什么有这两种存储图的方式呢？肯定是因为他们有不同的适用场景。

注意分析两种存储方式的空间复杂度，对于一幅有 `V` 个节点，`E` 条边的图，邻接表的空间复杂度是 O(V+E)*O*(*V*+*E*)，而邻接矩阵的空间复杂度是 O(V2)*O*(*V*2)。

所以如果一幅图的 `E` 远小于 `V^2`（稀疏图），那么邻接表会比邻接矩阵节省空间，反之，如果 `E` 接近 `V^2`（稠密图），二者就差不多了。

在后面的图算法和习题中，大多都是稀疏图，所以你会看到邻接表的使用更多一些。

邻接矩阵的最大优势在于，矩阵是一个强有力的数学工具，图的一些隐晦性质可以借助精妙的矩阵运算展现出来。不过本文不准备引入数学内容，所以有兴趣的读者可以自行搜索学习。

这也是为什么一定要把图节点类型转换成整数 id 的原因，不然的话你怎么用矩阵运算呢



### 不同种类的图结构

**其实，这些更复杂的模型都是基于这个最简单的图衍生出来的**。

**有向加权图怎么实现**？很简单呀：

如果是邻接表，我们不仅仅存储某个节点 `x` 的所有邻居节点，还存储 `x` 到每个邻居的权重，不就实现加权有向图了吗？

如果是邻接矩阵，`matrix[x][y]` 不再是布尔值，而是一个 int 值，0 表示没有连接，其他值表示权重，不就变成加权有向图了吗？

如果用代码的形式来表现，大概长这样：

```java
// 邻接表
// graph[x] 存储 x 的所有邻居节点以及对应的权重
// 具体实现不一定非得这样，可以参考后面的通用实现
class Edge {
    int to;
    int weight;
}
List<Edge>[] graph;

// 邻接矩阵
// matrix[x][y] 记录 x 指向 y 的边的权重，0 表示不相邻
int[][] matrix;
```

**无向图怎么实现**？也很简单，所谓的「无向」，是不是等同于「双向」？

<img src="https://labuladong.online/images/algo/graph/3.jpeg" alt="img" style="zoom: 25%;" />

如果连接无向图中的节点 `x` 和 `y`，把 `matrix[x][y]` 和 `matrix[y][x]` 都变成 `true` 不就行了；邻接表也是类似的操作，在 `x` 的邻居列表里添加 `y`，同时在 `y` 的邻居列表里添加 `x`。

把上面的技巧合起来，就变成了无向加权图……

好了，关于图的基本介绍就到这里，现在不管来什么乱七八糟的图，你心里应该都有底了。

下面我写一个通用的类，来实现图的基本操作（增删查改）。



## 图结构的通用代码实现

基于上面的讲解，我们可以抽象出一个 `Graph` 接口，来实现图的基本增删查改：

```java
interface Graph {
    // 添加一条边（带权重）
    void addEdge(int from, int to, int weight);

    // 删除一条边
    void removeEdge(int from, int to);

    // 判断两个节点是否相邻
    boolean hasEdge(int from, int to);

    // 返回一条边的权重
    int weight(int from, int to);

    // 返回某个节点的所有邻居节点和对应权重
    List<Edge> neighbors(int v);

    // 返回节点总数
    int size();
}
```

这其实是有向加权图的接口，但基于这个接口可以实现所有不同种类的无向/有向/无权/加权图。下面给出具体代码。

### 有向加权图（邻接表实现）

我这里给出一个简单的通用实现，后文图论算法教程和习题中可能会用到。其中有一些可以优化的点我写在注释中了。

> 注意
>
> 简单起见，下面给出的代码默认输入的都是合法的，不会出现不合法的节点 id，也不会输入重复的边。
>
> 在实际的题目中，我们一般也会先剔除掉不合法的数据，然后再进行建图。

```java
// 加权有向图的通用实现（邻接表）
class WeightedDigraph {
    // 存储相邻节点及边的权重
    public static class Edge {
        int to;
        int weight;

        public Edge(int to, int weight) {
            this.to = to;
            this.weight = weight;
        }
    }

    // 邻接表，graph[v] 存储节点 v 的所有邻居节点及对应权重
    private List<Edge>[] graph;

    public WeightedDigraph(int n) {
        // 我们这里简单起见，建图时要传入节点总数，这其实可以优化
        // 比如把 graph 设置为 Map<Integer, List<Edge>>，就可以动态添加新节点了
        graph = new List[n];
        for (int i = 0; i < n; i++) {
            graph[i] = new ArrayList<>();
        }
    }

    // 增，添加一条带权重的有向边，复杂度 O(1)
    public void addEdge(int from, int to, int weight) {
        graph[from].add(new Edge(to, weight));
    }

    // 删，删除一条有向边，复杂度 O(V)
    public void removeEdge(int from, int to) {
        for (int i = 0; i < graph[from].size(); i++) {
            if (graph[from].get(i).to == to) {
                graph[from].remove(i);
                break;
            }
        }
    }

    // 查，判断两个节点是否相邻，复杂度 O(V)
    public boolean hasEdge(int from, int to) {
        for (Edge e : graph[from]) {
            if (e.to == to) {
                return true;
            }
        }
        return false;
    }

    // 查，返回一条边的权重，复杂度 O(V)
    public int weight(int from, int to) {
        for (Edge e : graph[from]) {
            if (e.to == to) {
                return e.weight;
            }
        }
        throw new IllegalArgumentException("No such edge");
    }

    // 上面的 hasEdge、removeEdge、weight 方法遍历 List 的行为是可以优化的
    // 比如用 Map<Integer, Map<Integer, Integer>> 存储邻接表
    // 这样就可以避免遍历 List，复杂度就能降到 O(1)

    // 查，返回某个节点的所有邻居节点，复杂度 O(1)
    public List<Edge> neighbors(int v) {
        return graph[v];
    }

    public static void main(String[] args) {
        WeightedDigraph graph = new WeightedDigraph(3);
        graph.addEdge(0, 1, 1);
        graph.addEdge(1, 2, 2);
        graph.addEdge(2, 0, 3);
        graph.addEdge(2, 1, 4);

        System.out.println(graph.hasEdge(0, 1)); // true
        System.out.println(graph.hasEdge(1, 0)); // false

        graph.neighbors(2).forEach(edge -> {
            System.out.println(2 + " -> " + edge.to + ", wight: " + edge.weight);
        });
        // 2 -> 0, wight: 3
        // 2 -> 1, wight: 4

        graph.removeEdge(0, 1);
        System.out.println(graph.hasEdge(0, 1)); // false
    }
}
```



### 有向加权图（邻接矩阵实现）

具体请看代码和注释：

```java
import java.util.ArrayList;
import java.util.List;

// 加权有向图的通用实现（邻接矩阵）
public class WeightedDigraph {
    // 存储相邻节点及边的权重
    public static class Edge {
        int to;
        int weight;

        public Edge(int to, int weight) {
            this.to = to;
            this.weight = weight;
        }
    }


    // 邻接矩阵，matrix[from][to] 存储从节点 from 到节点 to 的边的权重
    // 0 表示没有连接
    private int[][] matrix;

    public WeightedDigraph(int n) {
        matrix = new int[n][n];
    }

    // 增，添加一条带权重的有向边，复杂度 O(1)
    public void addEdge(int from, int to, int weight) {
        matrix[from][to] = weight;
    }

    // 删，删除一条有向边，复杂度 O(1)
    public void removeEdge(int from, int to) {
        matrix[from][to] = 0;
    }

    // 查，判断两个节点是否相邻，复杂度 O(1)
    public boolean hasEdge(int from, int to) {
        return matrix[from][to] != 0;
    }

    // 查，返回一条边的权重，复杂度 O(1)
    public int weight(int from, int to) {
        return matrix[from][to];
    }

    // 查，返回某个节点的所有邻居节点，复杂度 O(V)
    public List<Edge> neighbors(int v) {
        List<Edge> res = new ArrayList<>();
        for (int i = 0; i < matrix[v].length; i++) {
            if (matrix[v][i] != 0) {
                res.add(new Edge(i, matrix[v][i]));
            }
        }
        return res;
    }

    public static void main(String[] args) {
        WeightedDigraph graph = new WeightedDigraph(3);
        graph.addEdge(0, 1, 1);
        graph.addEdge(1, 2, 2);
        graph.addEdge(2, 0, 3);
        graph.addEdge(2, 1, 4);

        System.out.println(graph.hasEdge(0, 1)); // true
        System.out.println(graph.hasEdge(1, 0)); // false

        graph.neighbors(2).forEach(edge -> {
            System.out.println(2 + " -> " + edge.to + ", wight: " + edge.weight);
        });
        // 2 -> 0, wight: 3
        // 2 -> 1, wight: 4

        graph.removeEdge(0, 1);
        System.out.println(graph.hasEdge(0, 1)); // false
    }
}
```

### 有向无权图（邻接表/邻接矩阵实现）

直接复用上面的 `WeightedDigraph` 类就行，把 `addEdge` 方法的权重参数默认设置为 1 就行了。比较简单，我就不写代码了。

### [¶](https://labuladong.online/zh/algo/data-structure-basic/graph-basic/#无向加权图邻接表邻接矩阵实现)无向加权图（邻接表/邻接矩阵实现）

无向加权图就等同于双向的有向加权图，所以直接复用上面用邻接表/领接矩阵实现的 `WeightedDigraph` 类就行了，只是在增加边的时候，要同时添加两条边：

```java
// 无向加权图的通用实现
class WeightedUndigraph {
    private WeightedDigraph graph;

    public WeightedUndigraph(int n) {
        graph = new WeightedDigraph(n);
    }

    // 增，添加一条带权重的无向边
    public void addEdge(int from, int to, int weight) {
        graph.addEdge(from, to, weight);
        graph.addEdge(to, from, weight);
    }

    // 删，删除一条无向边
    public void removeEdge(int from, int to) {
        graph.removeEdge(from, to);
        graph.removeEdge(to, from);
    }

    // 查，判断两个节点是否相邻
    public boolean hasEdge(int from, int to) {
        return graph.hasEdge(from, to);
    }

    // 查，返回一条边的权重
    public int weight(int from, int to) {
        return graph.weight(from, to);
    }

    // 查，返回某个节点的所有邻居节点
    public List<WeightedDigraph.Edge> neighbors(int v) {
        return graph.neighbors(v);
    }

    public static void main(String[] args) {
        WeightedUndigraph graph = new WeightedUndigraph(3);
        graph.addEdge(0, 1, 1);
        graph.addEdge(2, 0, 3);
        graph.addEdge(2, 1, 4);

        System.out.println(graph.hasEdge(0, 1)); // true
        System.out.println(graph.hasEdge(1, 0)); // true

        graph.neighbors(2).forEach(edge -> {
            System.out.println(2 + " <-> " + edge.to + ", wight: " + edge.weight);
        });
        // 2 <-> 0, wight: 3
        // 2 <-> 1, wight: 4

        graph.removeEdge(0, 1);
        System.out.println(graph.hasEdge(0, 1)); // false
        System.out.println(graph.hasEdge(1, 0)); // false
    }
}
```

### 无向无权图（邻接表/邻接矩阵实现）

直接复用上面的 `WeightedUndigraph` 类就行，把 `addEdge` 方法的权重参数默认设置为 1 就行了。比较简单，我就不写代码了。





# 图结构的 DFS/BFS 遍历

> 一句话总结
>
> 图的遍历就是 [多叉树遍历](https://labuladong.online/zh/algo/data-structure-basic/n-ary-tree-traverse-basic/) 的延伸，主要遍历方式还是深度优先搜索（DFS）和广度优先搜索（BFS）。
>
> 唯一的区别是，树结构中不存在环，而图结构中可能存在环，所以我们需要标记遍历过的节点，避免遍历函数在环中死循环。
>
> 由于图结构的复杂性，可以细分为遍历图的「节点」、「边」和「路径」三种场景，每种场景的代码实现略有不同。
>
> 遍历图的「节点」和「边」时，需要 `visited` 数组在前序位置做标记，避免重复遍历；遍历图的「路径」时，需要 `onPath` 数组在前序位置标记节点，在后序位置撤销标记。



## 深度优先搜索（DFS）

前文 [图结构基础和通用实现](https://labuladong.online/zh/algo/data-structure-basic/graph-basic/) 中说了，我们一般不用 `Vertex` 这样的类来存储图，但是这里我还是先用一下这个类，以便大家把图的遍历和多叉树的遍历做对比。后面我会给出基于邻接表/邻接矩阵的遍历代码。

### [¶](https://labuladong.online/zh/algo/data-structure-basic/graph-traverse-basic/#遍历所有节点visited-数组)遍历所有节点（`visited` 数组）

对比多叉树的遍历框架看图的遍历框架吧：

```java
// 多叉树节点
class Node {
    int val;
    List<Node> children;
}

// 多叉树的遍历框架
void traverse(Node root) {
    // base case
    if (root == null) {
        return;
    }
    // 前序位置
    System.out.println("visit " + root.val);
    for (Node child : root.children) {
        traverse(child);
    }
    // 后序位置
}


// 图节点
class Vertex {
    int id;
    Vertex[] neighbors;
}

// 图的遍历框架
// 需要一个 visited 数组记录被遍历过的节点
// 避免走回头路陷入死循环
void traverse(Vertex s, boolean[] visited) {
    // base case
    if (s == null) {
        return;
    }
    if (visited[s.id]) {
        // 防止死循环
        return;
    }
    // 前序位置
    visited[s.id] = true;
    System.out.println("visit " + s.id);
    for (Vertex neighbor : s.neighbors) {
        traverse(neighbor, visited);
    }
    // 后序位置
}
```

可以看到，图的遍历比多叉树的遍历多了一个 `visited` 数组，用来记录被遍历过的节点，避免遇到环时陷入死循环。

> 为什么成环会导致死循环
>
> 举个最简单的成环场景，有一条 `1 -> 2` 的边，同时有一条 `2 -> 1` 的边，节点 `1, 2` 就形成了一个环：
>
> ```
> 1 <=> 2
> ```
>
> 如果我们不标记遍历过的节点，那么从 `1` 开始遍历，会走到 `2`，再走到 `1`，再走到 `2`，再走到 `1`，如此 `1->2->1->2->...` 无限递归循环下去。
>
> 如果有了 `visited` 数组，第一次遍历到 `1` 时，会标记 `1` 为已访问，出现 `1->2->1` 这种情况时，发现 `1` 已经被访问过，就会直接返回，从而终止递归，避免了死循环。

有了上面的铺垫，就可以写出基于邻接表/邻接矩阵的图遍历代码了。虽然邻接表/邻接矩阵的底层存储方式不同，但提供了统一的 API，所以直接使用 [图结构基础和通用实现](https://labuladong.online/zh/algo/data-structure-basic/graph-basic/) 中那个 `Graph` 接口的方法即可

```java
// 遍历图的所有节点
void traverse(Graph graph, int s, boolean[] visited) {
    // base case
    if (s < 0 || s >= graph.size()) {
        return;
    }
    if (visited[s]) {
        // 防止死循环
        return;
    }
    // 前序位置
    visited[s] = true;
    System.out.println("visit " + s);
    for (Edge e : graph.neighbors(s)) {
        traverse(graph, e.to, visited);
    }
    // 后序位置
}
```

由于 `visited` 数组的剪枝作用，这个遍历函数会遍历一次图中的所有节点，并尝试遍历一次所有边，所以算法的时间复杂度是 O(E+V)*O*(*E*+*V*)，其中 `E` 是边的总数，`V` 是节点的总数。

> >  时间复杂度为什么是 `O(E + V)`？
>
> 我们之前讲解 [二叉树的遍历](https://labuladong.online/zh/algo/data-structure-basic/binary-tree-traverse-basic/) 时说，二叉树的遍历函数时间复杂度是 O(N)*O*(*N*)，其中 N*N* 是节点的总数。
>
> 这里图结构既然是树结构的延伸，为什么图的遍历函数时间复杂度是 O(E+V)*O*(*E*+*V*)，要把边的数量 E*E* 也算进去呢？为什么不是 O(V)*O*(*V*) 呢？
>
> 这是个非常好的问题。你可以花上两分钟想想，我把答案写在下面。

> 其实二叉树/多叉树的遍历函数，也要算上边的数量，只不过对于树结构来说，边的数量和节点的数量是近似相等的，所以时间复杂度还是 O(N+N)=O(N)
>
> 树结构中的边只能由父节点指向子节点，所以除了根节点，你可以把每个节点和它上面那条来自父节点的边配成一对儿，这样就可以比较直观地看出边的数量和节点的数量是近似相等的。
>
> 而对于图结构来说，任意两个节点之间都可以连接一条边，边的数量和节点的数量不再有特定的关系，所以我们要说图的遍历函数时间复杂度是 O(E+V)*O*(*E*+*V*)。



### 遍历所有边（二维 `visited` 数组）

对于图结构，遍历所有边的场景并不多见，主要是 [计算欧拉路径](https://labuladong.online/zh/algo/data-structure-basic/eulerian-graph/) 时会用到，所以这里简单提一下。

上面遍历所有节点的代码用一个一维的 `visited` 数组记录已经访问过的节点，确保每个节点只被遍历一次；那么最简单直接的实现思路就是用一个二维的 `visited` 数组来记录遍历过的边（`visited[u][v]` 表示边 `u->v` 已经被遍历过），从而确保每条边只被遍历一次。

先参考多叉树的遍历进行对比：

```java
// 多叉树节点
class Node {
    int val;
    List<Node> children;
}

// 遍历多叉树的树枝
void traverseBranch(Node root) {
    // base case
    if (root == null) {
        return;
    }
    for (Node child : root.children) {
        System.out.println("visit branch: " + root.val + " -> " + child.val);
        traverseBranch(child);
    }
}

// 图节点
class Vertex {
    int id;
    Vertex[] neighbors;
}

// 遍历图的边
// 需要一个二维 visited 数组记录被遍历过的边，visited[u][v] 表示边 u->v 已经被遍历过
void traverseEdges(Vertex s, boolean[][] visited) {
    // base case
    if (s == null) {
        return;
    }
    for (Vertex neighbor : s.neighbors) {
      // 如果边已经被遍历过，则跳过
      if (visited[s.id][neighbor.id]) {
        continue;
      }
      // 标记并访问边
      visited[s.id][neighbor.id] = true;
      System.out.println("visit edge: " + s.id + " -> " + neighbor.id);
      traverseEdges(neighbor, visited);
    }
}
```

> 提示
>
> 由于一条边由两个节点构成，所以我们需要把前序位置的相关代码放到 for 循环内部。



接下来，我们可以用 [图结构基础和通用实现](https://labuladong.online/zh/algo/data-structure-basic/graph-basic/) 中的 `Graph` 接口来实现：

```java
// 从起点 s 开始遍历图的所有边
void traverseEdges(Graph graph, int s, boolean[][] visited) {
    // base case
    if (s < 0 || s >= graph.size()) {
        return;
    }
    for (Edge e : graph.neighbors(s)) {
      // 如果边已经被遍历过，则跳过
      if (visited[s][e.to]) {
        continue;
      }
      // 标记并访问边
      visited[s][e.to] = true;
      System.out.println("visit edge: " + s + " -> " + e.to);
      traverseEdges(graph, e.to, visited);
    }
}
```

显然，使用二维 `visited` 数组并不是一个很高效的实现方式，因为需要创建二维 `visited` 数组，这个算法的时间复杂度是 O(E+V^2)，空间复杂度是 O(V^2)，其中 E*E* 是边的数量，V*V* 是节点的数量。

在讲解 [Hierholzer 算法计算欧拉路径](https://labuladong.online/zh/algo/data-structure/eulerian-graph-hierholzer/) 时，我们会介绍一种简单的优化避免使用二维 `visited` 数组，这里暂不展开。

### [¶](https://labuladong.online/zh/algo/data-structure-basic/graph-traverse-basic/#遍历所有路径onpath-数组)遍历所有路径（`onPath` 数组）

为啥要把图的这几种遍历都讲清楚？因为本站开篇就讲，一切算法的本质是穷举。只要你学会了穷举一切路径，就肯定会计算最短路径，这是图论中一类经典问题。

对于树结构，遍历所有「路径」和遍历所有「节点」是没什么区别的。而对于图结构，遍历所有「路径」和遍历所有「节点」稍有不同。

因为对于树结构来说，只能由父节点指向子节点，所以从根节点 `root` 出发，到任意一个节点 `targetNode` 的路径都是唯一的。换句话说，我遍历一遍树结构的所有节点之后，必然可以找到 `root` 到 `targetNode` 的唯一路径：

```java
// 多叉树的遍历框架，寻找从根节点到目标节点的路径
LinkedList<Node> path = new LinkedList<>();
void traverse(Node root, Node targetNode) {
    // base case
    if (root == null) {
        return;
    }
    if (root.val == targetNode.val) {
        // 找到目标节点
        System.out.println("find path: " + String.join("->", path) + "->" + targetNode);
        return;
    }
    // 前序位置
    path.addLast(root);
    for (Node child : root.children) {
        traverse(child, targetNode);
    }
    // 后序位置
    path.removeLast();
}
```

而对于图结构来说，由起点 `src` 到目标节点 `dest` 的路径可能不止一条。我们需要一个 `onPath` 数组，在进入节点时（前序位置）标记为正在访问，退出节点时（后序位置）撤销标记，这样才能遍历图中的所有路径，从而找到 `src` 到 `dest` 的所有路径：

```java
// 下面的算法代码可以遍历图的所有路径，寻找从 src 到 dest 的所有路径

// onPath 和 path 记录当前递归路径上的节点
boolean[] onPath = new boolean[graph.size()];
List<Integer> path = new LinkedList<>();

void traverse(Graph graph, int src, int dest) {
    // base case
    if (src < 0 || src >= graph.size()) {
        return;
    }
    if (onPath[src]) {
        // 防止死循环（成环）
        return;
    }
    if (src == dest) {
        // 找到目标节点
        System.out.println("find path: " + String.join("->", path) + "->" + dest);
        return;
    }

    // 前序位置
    onPath[src] = true;
    path.add(src);
    for (Edge e : graph.neighbors(src)) {
        traverse(graph, e.to, dest);
    }
    // 后序位置
    path.remove(path.size() - 1);
    onPath[src] = false;
}
```

> 关键区别在于后序位置撤销标记
>
> 为啥之前讲的遍历节点就不用撤销 `visited` 数组的标记，而这里要在后序位置撤销 `onPath` 数组的标记呢？
>
> 因为前文遍历节点的代码中，`visited` 数组的职责是保证每个节点只会被访问一次。**而对于图结构来说，要想遍历所有路径，可能会多次访问同一个节点，这是关键的区别**。
>
> 比方说下面这幅图，我们想求从节点 `1` 到节点 `4` 的全部路径：

<img src="https://labuladong.online/images/algo/ds-basic/graph-path.jpg" alt="img" style="zoom:33%;" />

如果使用前文遍历节点的算法，只在前序位置标记 `visited` 为 true，那么遍历完 `1->2->4` 和 `1->2->3->4` 之后，所有节点都已经被标记为已访问了，算法就会停止，`visited` 数组完成了自己的职责。

但是显然我们还没有遍历完所有路径，`1->3->2->4` 和 `1->3->4` 被漏掉了。

如果用 `onPath` 数组在离开节点时（后序位置）撤销标记，就可以找到 `1` 到 `4` 的所有路径。

由于这里使用的 `onPath` 数组会在后序位置撤销标记，所以这个函数可能重复遍历图中的节点和边，复杂度一般较高（阶乘或指数级），具体的时间复杂应该是所有路径的长度之和，取决于图的结构特点。

### [¶](https://labuladong.online/zh/algo/data-structure-basic/graph-traverse-basic/#同时使用-visited-和-onpath-数组)同时使用 `visited` 和 `onPath` 数组

按照上面的分析，`visited` 数组和 `onPath` 分别用于遍历所有节点和遍历所有路径。那么它们两个是否可能会同时出现呢？答案是可能的。

遍历所有路径的算法复杂度较高，大部分情况下我们可能并不需要穷举完所有路径，而是仅需要找到某一条符合条件的路径。这种场景下，我们可能会借助 `visited` 数组进行剪枝，提前排除一些不符合条件的路径，从而降低复杂度。

比如后文 [拓扑排序](https://labuladong.online/zh/algo/data-structure/topological-sort/) 中会讲到如何判定图是否成环，就会同时利用 `visited` 和 `onPath` 数组来进行剪枝。

比方说判定成环的场景，在遍历所有路径的过程中，如果发现一个节点 `s` 被标记为 `visited`，那么说明从 `s` 这个起点出发的所有路径在之前都已经遍历过了。如果之前遍历的时候都没有找到环，我现在再去遍历一次，肯定也不会找到环，所以这里可以直接剪枝，不再继续遍历节点 `s`。

我会在后面的图论算法和习题中结合具体的案例讲解，这里就不展开了。

### [¶](https://labuladong.online/zh/algo/data-structure-basic/graph-traverse-basic/#完全不用-visited-和-onpath-数组)完全不用 `visited` 和 `onPath` 数组

是否有既不用 `visited` 数组，也不用 `onPath` 数组的场景呢？其实也是有的。

前面介绍了，`visited` 和 `onPath` 主要的作用就是处理成环的情况，避免死循环。那如果题目告诉你输入的图结构不包含环，那么你就不需要考虑成环的情况了。

比如下面这个例题：

#### 力扣797.所有可能的路径

这个题目输入的是一个 [邻接表](https://labuladong.online/zh/algo/data-structure-basic/graph-basic/)，且明确告诉你输入的图结构不包含环，所以不需要 `visited` 和 `onPath` 数组，直接使用 DFS 遍历图就行了：

```java
class Solution {
    // 记录所有路径
    List<List<Integer>> res = new LinkedList<>();
    LinkedList<Integer> path = new LinkedList<>();

    public List<List<Integer>> allPathsSourceTarget(int[][] graph) {
        traverse(graph, 0);
        return res;
    }

    // 图的遍历框架
    void traverse(int[][] graph, int s) {

        // 添加节点 s 到路径
        path.addLast(s);

        int n = graph.length;
        if (s == n - 1) {
            // 到达终点
            res.add(new LinkedList<>(path));
            path.removeLast();
            return;
        }

        // 递归每个相邻节点
        for (int v : graph[s]) {
            traverse(graph, v);
        }

        // 从路径移出节点 s
        path.removeLast();
    }
}
```



## 广度优先搜索（BFS）

图结构的广度优先搜索其实就是 [多叉树的层序遍历](https://labuladong.online/zh/algo/data-structure-basic/n-ary-tree-traverse-basic/)，无非就是加了一个 `visited` 数组来避免重复遍历节点。

理论上 BFS 遍历也需要区分遍历所有「节点」和遍历所有「路径」，但是实际上 BFS 算法一般只用来寻找那条**最短路径**，不会用来求**所有路径**。

当然 BFS 算法肯定也可以求所有路径，但是我们一般会选择用 DFS 算法求所有路径，具体原因我在 [二叉树的递归/层序遍历](https://labuladong.online/zh/algo/data-structure-basic/use-case-of-dfs-bfs/) 中讲过，这里就不展开了。

那么如果只求最短路径的话，只需要遍历「节点」就可以了，因为按照 BFS 算法一层一层向四周扩散的逻辑，第一次遇到目标节点，必然就是最短路径。

和前文 [多叉树层序遍历](https://labuladong.online/zh/algo/data-structure-basic/n-ary-tree-traverse-basic/) 介绍的一样，图结构的 BFS 算法框架也有三种不同的写法，下面我会对比着多叉树的层序遍历写一下图结构的三种 BFS 算法框架。

### [¶](https://labuladong.online/zh/algo/data-structure-basic/graph-traverse-basic/#写法一)写法一

第一种写法是不记录遍历步数的。

多叉树的层序遍历写法是这样：

```java
void levelOrderTraverse(Node root) {
    if (root == null) {
        return;
    }
    Queue<Node> q = new LinkedList<>();
    q.offer(root);
    while (!q.isEmpty()) {
        Node cur = q.poll();
        // 访问 cur 节点
        System.out.println(cur.val);

        // 把 cur 的所有子节点加入队列
        for (Node child : cur.children) {
            q.offer(child);
        }
    }
}
```

图结构的 BFS 遍历是类似的：

```java
// 图结构的 BFS 遍历，从节点 s 开始进行 BFS
void bfs(Graph graph, int s) {
    boolean[] visited = new boolean[graph.size()];
    Queue<Integer> q = new LinkedList<>();
    q.offer(s);
    visited[s] = true;

    while (!q.isEmpty()) {
        int cur = q.poll();
        System.out.println("visit " + cur);
        for (Edge e : graph.neighbors(cur)) {
            if (visited[e.to]) { // [!code highlight:5]
                continue;
            }
            q.offer(e.to);
            visited[e.to] = true;
        }
    }
}
```

### 写法二

第二种能够记录遍历步数的写法。

多叉树的层序遍历写法是这样：

```java
void levelOrderTraverse(Node root) {
    if (root == null) {
        return;
    }
    Queue<Node> q = new LinkedList<>();
    q.offer(root);
    // 记录当前遍历到的层数（根节点视为第 1 层）
    int depth = 1;

    while (!q.isEmpty()) {
        int sz = q.size();
        for (int i = 0; i < sz; i++) {
            Node cur = q.poll();
            // 访问 cur 节点，同时知道它所在的层数
            System.out.println("depth = " + depth + ", val = " + cur.val);

            for (Node child : cur.children) {
                q.offer(child);
            }
        }
        depth++;
    }
}
```

图结构的 BFS 遍历是类似的：

```java
// 从 s 开始 BFS 遍历图的所有节点，且记录遍历的步数
void bfs(Graph graph, int s) {
    boolean[] visited = new boolean[graph.size()]; // [!code highlight]
    Queue<Integer> q = new LinkedList<>();
    q.offer(s);
    visited[s] = true; // [!code highlight]
    // 记录从 s 开始走到当前节点的步数
    int step = 0;
    while (!q.isEmpty()) {
        int sz = q.size();
        for (int i = 0; i < sz; i++) {
            int cur = q.poll();
            System.out.println("visit " + cur + " at step " + step);
            for (Edge e : graph.neighbors(cur)) {
                if (visited[e.to]) { // [!code highlight:5]
                    continue;
                }
                q.offer(e.to);
                visited[e.to] = true;
            }
        }
        step++;
    }
}
```

### 写法三

第三种能够适配不同权重边的写法。

多叉树的层序遍历写法是这样：

```java
// 多叉树的层序遍历
// 每个节点自行维护 State 类，记录深度等信息
class State {
    Node node;
    int depth;

    public State(Node node, int depth) {
        this.node = node;
        this.depth = depth;
    }
}

void levelOrderTraverse(Node root) {
    if (root == null) {
        return;
    }
    Queue<State> q = new LinkedList<>();
    // 记录当前遍历到的层数（根节点视为第 1 层）
    q.offer(new State(root, 1));

    while (!q.isEmpty()) {
        State state = q.poll();
        Node cur = state.node;
        int depth = state.depth;
        // 访问 cur 节点，同时知道它所在的层数
        System.out.println("depth = " + depth + ", val = " + cur.val);

        for (Node child : cur.children) {
            q.offer(new State(child, depth + 1));
        }
    }
}
```

图结构的 BFS 遍历是类似的：

```java
// 图结构的 BFS 遍历，从节点 s 开始进行 BFS，且记录遍历步数（从起点 s 到当前节点的边的条数）
// 每个节点自行维护 State 类，记录从 s 走来的遍历步数
class State {
    // 当前节点 ID
    int node;
    // 从起点 s 到当前节点的遍历步数
    int step;

    public State(int node, int step) {
        this.node = node;
        this.step = step;
    }
}


void bfs(Graph graph, int s) {
    boolean[] visited = new boolean[graph.size()];
    Queue<State> q = new LinkedList<>();

    q.offer(new State(s, 0));
    visited[s] = true;

    while (!q.isEmpty()) {
        State state = q.poll();
        int cur = state.node;
        int step = state.step;
        System.out.println("visit " + cur + " with step " + step);
        for (Edge e : graph.neighbors(cur)) {
            if (visited[e.to]) { // [!code highlight:5]
                continue;
            }
            q.offer(new State(e.to, step + 1));
            visited[e.to] = true;
        }
    }
}
```

上面的代码示例中，`State.step` 变量记录了从起点 `s` 到当前节点的最少步数（边的条数）。

但是对于加权图来说，每条边可以有不同的权重，题目一般会让我们计算从 `src` 到 `dest` 的权重和最小的路径。在这个场景中，`step` 的值（边的条数）失去了意义，这个算法也无法完成任务。

我们会在之后的 [图结构最短路径算法](https://labuladong.online/zh/algo/data-structure-basic/graph-shortest-path/) 中详细介绍如何计算加权图的最小权重路径，那时候你就会发现，只需要对这个 BFS 写法稍作修改就能得到 [Dijkstra 算法](https://labuladong.online/zh/algo/data-structure/dijkstra/)，完成加权图的最短路径计算。