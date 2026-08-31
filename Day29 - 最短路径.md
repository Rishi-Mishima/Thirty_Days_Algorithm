<img src="/Users/ruixue/Library/Application Support/typora-user-images/image-20260728135208531.png" alt="image-20260728135208531" style="zoom:33%;" />

Dijkstra 算法是一种用于计算图中单源最短路径的算法，**其本质是标准 BFS 算法 + 贪心思想**。

如果图中包含负权重边，会让贪心思想失效，所以 **Dijkstra 只能处理不包含负权重边的图**。

Dijkstra 算法和标准的 BFS 算法的区别只有两个：

1、标准 BFS 算法使用普通队列，Dijkstra 算法使用优先级队列，让距离起点更近的节点优先出队（贪心思想的体现）。

2、标准 BFS 算法使用一个 `visited` 数组记录访问过的节点，确保算法不会陷入死循环；Dijkstra 算法使用一个 `distTo` 数组，确保算法不会陷入死循环，同时记录起点到其他节点的最短路径。

在 [图结构最短路径算法概览](https://labuladong.online/zh/algo/data-structure-basic/graph-shortest-path/) 中我们已经简要介绍了图结构中的最短路径问题、单源最短路径和多源最短路径的区别、负权重边产生的问题、以及包括 Dijkstra 算法在内的几种经典最短路径算法，本文会包含以下内容：

1、基于标准的 [图结构 BFS 遍历算法](https://labuladong.online/zh/algo/data-structure-basic/graph-traverse-basic/) 拓展出 Dijkstra 算法的代码。

2、详细介绍 Dijkstra 算法的实现原理，并证明算法的正确性。

3、点到点最短路径问题是单源最短路径的一个特例，对标准的 Dijkstra 算法代码稍作修改即可优化效率。

掌握原理和代码模板后，下篇文章中会修改标准 Dijkstra 算法代码，解决更复杂的多约束条件下的最短路径问题。

本文会用到 [图结构基础及通用实现](https://labuladong.online/zh/algo/data-structure-basic/graph-basic/) 中定义的图结构接口，请先阅读相关章节了解图结构的存储方法和接口定义。

## Dijkstra 算法代码

[图结构 BFS 遍历算法](https://labuladong.online/zh/algo/data-structure-basic/graph-traverse-basic/) 中介绍了三种 BFS 写法，其中第三种写法就特别适合 Dijkstra 算法，因为我们需要维护从起点到当前遍历的节点的最短路径。

对比来看，这是标准的图结构 BFS 遍历算法：

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

这个算法中，我们用 `State.step` 记录从起点到当前节点的最少步数（边的条数），并使用 `visited` 数组记录访问过的节点，保证每个节点只会被访问一次（即入队和出队一次），避免算法进入死循环。

在加权图场景中，最短路径问题想要计算的是从起点到其他节点的最小路径权重和，因为每条边可以有不同的权重，所以上述算法中计算的最少步数（边的条数）已经没有意义了，并不能得到权重和最小的路径。

这是 Dijkstra 算法代码，其中不同的地方高亮标记了：

```java
class State {
    // 当前节点 ID
    int node;
    // 从起点 s 到当前 node 节点的最小路径权重和
    int distFromStart;

    public State(int node, int distFromStart) {
        this.node = node;
        this.distFromStart = distFromStart;
    }
}

// 输入不包含负权重边的加权图 graph 和起点 src
// 返回从起点 src 到其他节点的最小路径权重和
int[] dijkstra(Graph graph, int src) {
    // 记录从起点 src 到其他节点的最小路径权重和
    // distTo[i] 表示从起点 src 到节点 i 的最小路径权重和
    int[] distTo = new int[graph.size()];
    // 都初始化为正无穷，表示未计算
    Arrays.fill(distTo, Integer.MAX_VALUE);

    // 优先级队列，distFromStart 较小的节点排在前面
    Queue<State> pq = new PriorityQueue<>((a, b) -> {
        return a.distFromStart - b.distFromStart;
    });

    // 从起点 src 开始进行 BFS
    pq.offer(new State(src, 0));
    distTo[src] = 0;

    while (!pq.isEmpty()) {
        State state = pq.poll();
        int curNode = state.node;
        int curDistFromStart = state.distFromStart;

        if (distTo[curNode] < curDistFromStart) { // [!code highlight:5]
            // 在 Dijkstra 算法中，队列中可能存在重复的节点 state
            // 所以要在元素出队时进行判断，去除较差的重复节点
            continue;
        }

        for (Edge e : graph.neighbors(curNode)) {
            int nextNode = e.to;
            int nextDistFromStart = curDistFromStart + e.weight;

            if (distTo[nextNode] <= nextDistFromStart) {
                continue;
            }

            // [!code highlight:6]
            // 将 nextNode 节点加入优先级队列
            pq.offer(new State(nextNode, nextDistFromStart));
            // 记录 nextNode 节点到起点的最小路径权重和
            distTo[nextNode] = nextDistFromStart;
        }
    }

    return distTo;
}
```



## Dijkstra 和 BFS 的关键区别

Dijkstra 算法和标准 BFS 算法的逻辑几乎完全相同，主要的修改如下：

1、给 `State` 类增加一个 `distFromStart` 字段，用于记录从起点到当前节点的路径权重和。使用 [优先级队列](https://labuladong.online/zh/algo/data-structure-basic/binary-heap-basic/) 代替普通队列，让 `distFromStart` 最小的节点优先出队。

2、用 `distTo` 数组替代 `visited` 数组。标准 BFS 算法中，是仅当 `visited[node] == false` 时才会让节点入队；Dijkstra 算法中是仅当节点能够让 `distTo[node]` 更小的时候才会让节点 State 入队。

3、在元素出队时，对 `distTo[curNode] < curDistFromStart` 的情况进行剪枝。

**先来讲解第一个区别，为啥要用优先级队列**？

主要是为了提升算法效率。算法的效率取决于入队的元素总量，入队的元素越少，那么搜索次数越少，算法效率越高。

而我们仅允许 `state.distFromStart` 小于 `distTo[state.node]` 的节点 state 入队

```java
if (distTo[nextNode] <= nextDistFromStart) {
    continue;
}
// 仅允许 nextDistFromStart < distTo[nextNode] 的节点入队
pq.offer(new State(nextNode, nextDistFromStart));
distTo[nextNode] = nextDistFromStart;
```

如果使用了优先级队列，最先出队的 `state.distFromStart` 必然很小，`distTo[state.node]` 就会很小，这就能避免很多非最优的节点 state 入队，从而提升搜索效率。

**接下来讲第二个区别，为啥要用 `distTo` 数组替代 `visited` 数组**？

关键在于，标准 BFS 算法中路径权重和边的条数是等价的，每条边的权重都相当于是 1，所以边的条数就等于路径的权重和。

按照 BFS 逐层向外扩散搜索的逻辑，第一次到达节点 `i` 时，就等于找到了最短路径。

而在加权图中，路径权重和与边的条数没有什么关系了，先被搜索到的节点可能路径权重和很大，后被搜索到的节点可能路径权重和很小，所以需要一个 `distTo` 数组来记录每个节点的已知最短路径权重和，如果遇到更短的路径，就更新 `distTo[node]` 的值。

**最后讲第三个区别，也是最重要的一个区别，为什么要在元素出队时进行剪枝**？

```java
// 元素出队时，进行剪枝
int curNode = state.node;
int curDistFromStart = state.distFromStart;
if (distTo[curNode] < curDistFromStart) {
    continue;
}
```

关键原因是，Dijkstra 算法的队列中可能有重复的节点 state。

举个例子就明白了，假设我们以节点 `1` 作为起点执行 Dijkstra 算法，第一步搜索，应该向优先级队列中添加 `State(4, 2)` 和 `State(3, 7)`（`1->4` 和 `1->3` 两条路径），同时更新 `distTo[4] = 2` 和 `distTo[3] = 7`：

<img src="https://labuladong.online/images/algo/dijkstra/d1.jpg" alt="img" style="zoom:33%;" />

然后 `State(4, 2)` 出队，找到第一条最短路径 `1->4`，此时优先级队列中剩下 `State(3, 7)`。

接下来将节点 `4` 的相邻节点加入优先级队列，假设是下图这样，存在一条 `4->3` 的边：

<img src="https://labuladong.online/images/algo/dijkstra/d5.jpg" alt="img" style="zoom:33%;" />

节点 `3` 对应的 `State(3, 3)` 会入队，并更新 `distTo[3] = 3`：

注意看此时优先级队列中存在两条到达节点 `3` 的路径，分别是 `1->4->3` 和 `1->3`，对应的 `State` 分别是 `State(3, 3)` 和 `State(3, 7)`。

`State(3, 3)` 排在前面先出队，得到 `1->4->3` 这条正确的最短路径，并将相邻节点入队。

等到 `State(3, 7)` 出队时，由于 `distTo[3] < 7`，会直接跳过，停止搜索。

**综上，我们应该在节点出队时进行剪枝判断，否则算法会反复扩散那些已经被更短路径更新过的过期 state，白白做很多无用功，拖慢效率**（算法能终止是靠入队时的 `distTo` 剪枝保证的，即便去掉这步出队剪枝也不会死循环，只是会慢很多）。

另外，这个例子还给我们了几个重要的观察：

- **当一个节点入队时，虽然更新了 `distTo[node]` 的值，但这个值并不一定是最短路径权重和，可能还存在更短的路径**。
- **当一个节点出队时，`state.distFromStart` 不一定是最短路径，而 `distTo[state.node]` 一定是最终的最短路径**。



> 入队剪枝 vs 出队剪枝

细心的读者可能注意到，出队时的剪枝用的是 `<`（严格小于），而入队时用的是 `<=`（小于等于），为什么会有这个区别？

```java
// 出队剪枝：严格小于
if (distTo[curNode] < curDistFromStart) {
    continue;
}

// 入队剪枝：小于等于
if (distTo[nextNode] <= nextDistFromStart) {
    continue;
}
```

因为 `==` 在这两个位置的含义完全不同：

**入队时 `distTo[nextNode] == nextDistFromStart`，意味着「队列中已经有重复的 State 了」，应该跳过**。

因为入队时会同步执行 `distTo[nextNode] = nextDistFromStart`，所以 `distTo[nextNode]` 等于某个值，就说明之前已经有一个携带同样距离的 state 入过队了，那个 state 还在队列里等着扩散（或者已经扩散过了），再入队一个一模一样的 state 纯属多余，应该跳过。

当然，如果你不跳过（用 `<=`），也不影响正确性，但是会增加队列中的冗余元素，降低算法性能。

**出队时 `distTo[curNode] == curDistFromStart`，意味着没找到其他更短的路径，该 State 记录的路径就是到达该节点的最短路径**，所以应该让这个节点扩散邻居节点，否则算法就会出错。



## 复杂度分析

假设输入的图结构有 V*V* 个节点，E*E* 条边，我们参照 [时空复杂度分析方法](https://labuladong.online/zh/algo/essential-technique/complexity-analysis/) 来分析 Dijkstra 算法的时间复杂度和空间复杂度。

Dijkstra 算法的空间复杂度由 `distTo` 数组和优先级队列 `pq` 最多存储的的节点 state 数量决定。

Dijkstra 算法的时间复杂度分析需要一些技巧，虽然看代码有嵌套的 for/while 循环，但算法的本质还是若干次 `pq` 的出队入队操作，所以时间复杂度取决于 `pq` 中存储的节点 state 数量和操作次数。

所以现在的关键问题就是，`pq` 中最多会存储多少个节点 state？我们之前探讨过，队列中可能出现重复的节点 state，所以不应该是 O(V) 个。

你可以仔细思考一下上面的例子，之所以节点 `3` 会出现 2 个重复的节点 state，是因为它的 [入度](https://labuladong.online/zh/algo/data-structure-basic/graph-basic/) 为 2，即有 2 条边指向节点 `3`：

<img src="https://labuladong.online/images/algo/dijkstra/d5.jpg" alt="img" style="zoom:33%;" />

换句话说，`pq` 中的元素数量不应该和节点数 V*V* 成正比，而应该和边数 E*E* 成正比，所以 `pq` 中最坏情况下可能存储的 state 数量为 O(E)*O*(*E*)。

综上，`pq` 中最多会存储 O(E) 个节点 state，每次出队入队操作的时间复杂度为 O(logE)，整体的**时间复杂度就是 O(ElogE)，空间复杂度是 O(V+E)**。





## 点对点最短路径的优化

上面给出的 Dijkstra 算法代码可以计算从起点 `src` 到其他所有节点的最短路径。

有些算法题中仅需要计算从起点 `src` 到某个终点 `dst` 的最短路径，上面的代码当然也可以完成这个任务，不过我们可以稍作修改，让算法的效率提高一些。

上面已经证明，当节点 State 出队时，`distTo[state.node]` 一定是最终的最短路径。所以可以在节点出队时加一个 `state.node == dst` 的条件判断，让算法提前结束：

```java
// 计算 src 到 dst 的最短路径权重和
int dijkstra(Graph graph, int src, int dst) {
    while (!pq.isEmpty()) {
        State state = pq.poll();
        int curNode = state.node;
        int curDistFromStart = state.distFromStart;

        if (distTo[curNode] < curDistFromStart) {
            continue;
        }
        // 节点出队时进行判断，遇到终点时直接返回
        if (curNode == dst) {
            return curDistFromStart;
        }
        ...

    }
    return -1;
}
```

- 空间复杂度, 时间复杂度 不变



## 总结

Dijkstra 的本质是 BFS 算法 + 贪心思想。借助优先级队列，最先出队的节点记录的 `distFromStart` 就是从起点到该节点的最小路径权重和。

贪心思想的正确性依赖前提：路径中每增加一条边，路径权重和应该会增加。

如果图中存在负权重边，这个前提就不成立，贪心思想失效，所以 Dijkstra 算法也会失效。

下一篇 [Dijkstra 算法拓展](https://labuladong.online/zh/algo/data-structure/dijkstra-follow-up/) 会介绍带限制条件的 Dijkstra 算法，[Dijkstra 算法习题章节](https://labuladong.online/zh/algo/problem-set/dijkstra/) 会带你运用 Dijkstra 算法解决几个有趣的问题。