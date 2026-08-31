

### 力扣102 二叉树的层序遍历 (HOT100)

广度优先搜索

<img src="https://pic.leetcode.cn/a58477c74c96779c265ce3028def7625d16042895d2c21f7fb0293df7b213276-Picture1.png" alt="Picture1.png" style="zoom:33%;" />

算法流程：

1. 特例处理： 当根节点为空，则返回空列表 [] 。
2. 初始化： 打印结果列表 res = [] ，包含根节点的队列 queue = [root] 。
3. BFS 循环： 当队列 queue 为空时跳出。
   1. 新建一个临时列表 tmp ，用于存储当前层打印结果。
   2. 当前层打印循环： 循环次数为当前层节点数（即队列 queue 长度）。
      1. 出队： 队首元素出队，记为 node。
      2. 打印： 将 node.val 添加至 tmp 尾部。
      3. 添加子节点： 若 node 的左（右）子节点不为空，则将左（右）子节点加入队列 queue 。
   3. 将当前层结果 tmp 添加入 res 。
4. 返回值： 返回打印结果列表 res 即可

作者：Krahets
链接：https://leetcode.cn/problems/binary-tree-level-order-traversal/solutions/2361604/102-er-cha-shu-de-ceng-xu-bian-li-yan-du-dyf7/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。



##### 方法一: 递归实现

- 相同层次的节点归入一个数组
- 传入辅助的Level参数来决定层次

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> ans = new ArrayList<>();
        if(root==null){
            return ans;
        }
        levelOrderHelper(root,ans,1);
        return ans;
    }

    public void levelOrderHelper(TreeNode root,List<List<Integer>> ans,int level){
        if(root == null){
            return;
        }
        List<Integer> tempAns = new ArrayList<>();
        if(ans.size()<level){
            ans.add(tempAns);
        }
        ans.get(level-1).add(root.val);
        levelOrderHelper(root.left,ans,level+1);
        levelOrderHelper(root.right,ans,level+1);
    }
}
```



##### 方法二: 迭代

- 层次遍历
- 模型识别: 一旦出现树的层次遍历,都可以用队列来辅助结构



```java
import java.util.*;	
class Solution {
	public List<List<Integer>> levelOrder(TreeNode root) {
		if(root==null) {
			return new ArrayList<List<Integer>>();
		}
		
		List<List<Integer>> res = new ArrayList<List<Integer>>();
		LinkedList<TreeNode> queue = new LinkedList<TreeNode>();
		//将根节点放入队列中，然后不断遍历队列
		queue.add(root);
		while(queue.size()>0) {
			//获取当前队列的长度，这个长度相当于 当前这一层的节点个数
			int size = queue.size();
			ArrayList<Integer> tmp = new ArrayList<Integer>();
			//将队列中的元素都拿出来(也就是获取这一层的节点)，放到临时list中
			//如果节点的左/右子树不为空，也放入队列中
			for(int i=0;i<size;++i) {
				TreeNode t = queue.remove();
				tmp.add(t.val);
				if(t.left!=null) {
					queue.add(t.left);
				}
				if(t.right!=null) {
					queue.add(t.right);
				}
			}
			//将临时list加入最终返回结果中
			res.add(tmp);
		}
		return res;
	}
}

作者：王尼玛
链接：https://leetcode.cn/problems/binary-tree-level-order-traversal/solutions/85075/die-dai-di-gui-duo-tu-yan-shi-102er-cha-shu-de-cen/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```





### BFS VS DFS

DFS 遍历使用 **递归** : 

```java
void dfs(TreeNode root) {
    if (root == null) {
        return;
    }
    dfs(root.left);
    dfs(root.right);
}
```



BFS 遍历使用**队列**数据结构:

```java
void bfs(TreeNode root) {
    Queue<TreeNode> queue = new ArrayDeque<>();
    queue.add(root);
    while (!queue.isEmpty()) {
        TreeNode node = queue.poll(); // Java 的 pop 写作 poll()
        if (node.left != null) {
            queue.add(node.left);
        }
        if (node.right != null) {
            queue.add(node.right);
        }
    }
}
```

DFS 遍历的代码比 BFS 简洁太多了！这是因为递归的方式隐含地使用了系统的 栈，我们不需要自己维护一个数据结构。如果只是简单地将二叉树遍历一遍，那么 DFS 显然是更方便的选择。

虽然 DFS 与 BFS 都是将二叉树的所有结点遍历了一遍，但它们遍历结点的顺序不同。

作者：nettee
链接：https://leetcode.cn/problems/binary-tree-level-order-traversal/solutions/244853/bfs-de-shi-yong-chang-jing-zong-jie-ceng-xu-bian-l/

这个遍历顺序也是 BFS 能够用来解「层序遍历」、「最短路径」问题的根本原因。



##### LeetCode 102. Binary Tree Level Order Traversal 二叉树的层序遍历（Medium）

给定一个二叉树，返回其按层序遍历得到的节点值。 层序遍历即逐层地、从左到右访问所有结点。

什么是层序遍历呢？简单来说，层序遍历就是把二叉树分层，然后每一层从左到右遍历：

![二叉树的层序遍历](https://pic.leetcode.cn/ce41cf1cabfa7a56387f63d927c8819fe1479ecf6f193a2a1b47964f5a8d1c8e.jpg)

乍一看来，这个遍历顺序和 BFS 是一样的，我们可以直接用 BFS 得出层序遍历结果。然而，层序遍历要求的输入结果和 BFS 是不同的。层序遍历要求我们区分每一层，也就是返回一个二维数组。而 BFS 的遍历结果是一个一维数组，无法区分每一层。



![BFS 遍历与层序遍历的输出结果不同](https://pic.leetcode.cn/fd1d63037d0e2f787d2140fee406e109094a4f66ab0837a7273f8b371eef8096.jpg)

那么，怎么给 BFS 遍历的结果分层呢？我们首先来观察一下 BFS 遍历的过程中，结点进队列和出队列的过程：

- 截取 BFS 遍历过程中的某个时刻：

![BFS 遍历中某个时刻队列的状态](https://pic.leetcode.cn/9f178b56ff1c94388d893f2fb48e9e77e186aba7cfd7483637776359062d68b8.jpg)

可以看到，此时队列中的结点是 3、4、5，分别来自第 1 层和第 2 层。这个时候，第 1 层的结点还没出完，第 2 层的结点就进来了，而且两层的结点在队列中紧挨在一起，我们 无法区分队列中的结点来自哪一层。

因此，我们需要稍微修改一下代码，在每一层遍历开始前，先记录队列中的结点数量 n（也就是这一层的结点数量），然后一口气处理完这一层的 n 个结点

```java
// 二叉树的层序遍历
void bfs(TreeNode root) {
    Queue<TreeNode> queue = new ArrayDeque<>();
    queue.add(root);
    while (!queue.isEmpty()) {
        int n = queue.size();
        for (int i = 0; i < n; i++) { 
            // 变量 i 无实际意义，只是为了循环 n 次
            TreeNode node = queue.poll();
            if (node.left != null) {
                queue.add(node.left);
            }
            if (node.right != null) {
                queue.add(node.right);
            }
        }
    }
}

```

在 while 循环的每一轮中，都是将当前层的所有结点出队列，再将下一层的所有结点入队列，这样就实现了层序遍历



最终代码

```java
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> res = new ArrayList<>();

    Queue<TreeNode> queue = new ArrayDeque<>();
    if (root != null) {
        queue.add(root);
    }
    while (!queue.isEmpty()) {
        int n = queue.size();
        List<Integer> level = new ArrayList<>();
        for (int i = 0; i < n; i++) { 
            TreeNode node = queue.poll();
            level.add(node.val);
            if (node.left != null) {
                queue.add(node.left);
            }
            if (node.right != null) {
                queue.add(node.right);
            }
        }
        res.add(level);
    }

    return res;
}

```



##### chatgpt (BFS)

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
      
      	//result 用来保存最终结果。
        List<List<Integer>> result = new ArrayList<>();

      	//处理空树
        if (root == null) {
            return result;
        }

      	// 创建队列
      	// 队列保存的是节点，而不是节点的值。
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root); //把根节点放进队列。

      	// 只要队列里还有节点，就继续处理。
      	//while 每循环一次，处理完整的一层。
        while (!queue.isEmpty()) {
          
          	// size 使他能保证一次只处理一层
          	//在处理当前层之前，记录当前队列里有多少个节点。
          	// 假如现在 queue = [9, 20], 说明当前层有两个节点, 因此size = 2
          	//接下来 for 循环只处理两个节点, 虽然处理这两个节点时，可能会把下一层的孩子加入队列，但本轮不会处理那些新加入的孩子。 这就是分层的关键
            int size = queue.size(); 
          
          	// 创建当前层的列表
          	//每进入一次 while，就创建一个新的 level
          	// 因为level每次都是新的,  所以 level 只保存当前层。
            List<Integer> level = new ArrayList<>();

          	// 内层 for：处理当前层所有节点
          	//size 是进入当前层时记录下来的节点数
          	// for 循环负责把这一层的所有节点全部处理完。
            for (int i = 0; i < size; i++) {
              
              	//从队列中取出当前节点; 取出队首节点, 然后从队列中删除
              	// 如果队列中之前是queue = [9,20], 执行了poll之后, node = 9, queue = [20]
                TreeNode node = queue.poll();
              	//将当前节点值加入当前层, node = 9, level = [9], 接着取出 20 level = [9, 20]
                level.add(node.val);

              // 把孩子节点加入队列
              // 如果左孩子存在，就把左孩子加入队尾。
                if (node.left != null) {
                    queue.offer(node.left);
                }

               // 如果右孩子存在，就把右孩子加入队尾。
                if (node.right != null) {
                    queue.offer(node.right);
                }
              
              // 注意顺序：先Left, 然后Right, 所以同一层会从左到右访问。
            }

          // 当前层完成
            result.add(level);
        }

        return result;
    }
}
```



###### 队列操作

```java
queue.offer(node); // 入队
queue.poll();      // 出队
queue.peek();      // 查看队首但不删除
queue.isEmpty();   // 队列是否为空
queue.size();      // 队列元素数量
```

`poll()` 会：

1. 取出队首节点；
2. 把这个节点从队列中删除。



> 易错点, `queue.size()` 会在循环中不断变化

它们在普通 `LinkedList` 中基本都能正常使用，但在容量受限的队列中：

- `add()` 失败时可能抛异常；
- `offer()` 失败时返回 `false`。

刷题中记住下面这组即可：

```java
offer() // 入队
poll()  // 出队
peek()  // 查看队首
```



时间复杂度: 

每个节点：

- 入队一次；
- 出队一次；
- 访问一次。

假设节点总数为 `n`：O(N)

> 我使用 BFS 和队列完成层序遍历。首先把根节点加入队列。每次进入 while 循环时，先记录当前队列大小 `size`，这个大小就是当前层的节点数量。然后循环 `size` 次，依次取出当前层的节点，记录其值，并把它们的左右孩子加入队列。新加入的孩子会留到下一轮 while 中处理，因此可以保证结果按层分组

记录当前层大小
→ 取出当前层节点
→ 孩子加入队列
→ 当前层加入结果



##### 最简单记忆模板

```java
List<List<Integer>> result = new ArrayList<>();
Queue<TreeNode> queue = new LinkedList<>();

queue.offer(root);

while (!queue.isEmpty()) {
    int size = queue.size();
    List<Integer> level = new ArrayList<>();

    for (int i = 0; i < size; i++) {
        TreeNode node = queue.poll();

        level.add(node.val);

        if (node.left != null) {
            queue.offer(node.left);
        }

        if (node.right != null) {
            queue.offer(node.right);
        }
    }

    result.add(level);
}
```





##### chatgpt (DFS)

力扣 102 也能使用 DFS，通过记录深度，把节点加入对应层：

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> result = new ArrayList<>();
        dfs(root, 0, result);
        return result;
    }

    private void dfs(
        TreeNode root,
        int depth,
        List<List<Integer>> result
    ) {
        if (root == null) {
            return;
        }

        if (result.size() == depth) {
            result.add(new ArrayList<>());
        }

        result.get(depth).add(root.val);

        dfs(root.left, depth + 1, result);
        dfs(root.right, depth + 1, result);
    }
}
```

不过面试中问“层序遍历”时，通常优先写 BFS，因为：

> 层序遍历本身就是 BFS 的典型应用。

102 最重要的是掌握队列版本。





#### BFS 应用二: (最短路径)

在一棵树中，一个结点到另一个结点的路径是唯一的，但在图中，结点之间可能有多条路径，其中哪条路最近呢？这一类问题称为 最短路径问题。最短路径问题也是 BFS 的典型应用，而且其方法与层序遍历关系密切。

在二叉树中，BFS 可以实现一层一层的遍历。在图中同样如此。从源点出发，BFS 首先遍历到第一层结点，到源点的距离为 1，然后遍历到第二层结点，到源点的距离为 2…… 可以看到，用 BFS 的话，距离源点更近的点会先被遍历到，这样就能找到到某个点的最短路径了

![层序遍历与最短路径](https://pic.leetcode.cn/01a3617511b1070216582ae59136888072116ccba360ab7c2aa60fc273351b85.jpg)

```java
小贴士：

很多同学一看到「最短路径」，就条件反射地想到「Dijkstra 算法」。为什么 BFS 遍历也能找到最短路径呢？

这是因为，Dijkstra 算法解决的是带权最短路径问题，而我们这里关注的是无权最短路径问题。也可以看成每条边的权重都是 1。这样的最短路径问题，用 BFS 求解就行了。

在面试中，你可能更希望写 BFS 而不是 Dijkstra。毕竟，敢保证自己能写对 Dijkstra 算法的人不多。
```

最短路径问题属于图算法。由于图的表示和描述比较复杂，本文用比较简单的网格结构代替。网格结构是一种特殊的图，它的表示和遍历都比较简单，适合作为练习题。在 LeetCode 中，最短路径问题也以网格结构为主。



##### [LeetCode 1162. As Far from Land as Possible](https://leetcode.cn/problems/as-far-from-land-as-possible/) 离开陆地的最远距离（Medium）

这道题就是一个在网格结构中求最短路径的问题。同时，它也是一个「岛屿问题」，即用网格中的 1 和 0 表示陆地和海洋，模拟出若干个岛屿。

在上一篇文章中，我们介绍了网格结构的基本概念，以及网格结构中的 DFS 遍历。其中一些概念和技巧也可以用在 BFS 遍历中：

格子 (r, c) 的相邻四个格子为：(r-1, c)、(r+1, c)、(r, c-1) 和 (r, c+1)；
使用函数 inArea 判断当前格子的坐标是否在网格范围内；
将遍历过的格子标记为 2，避免重复遍历。
对于网格结构的性质、网格结构的 DFS 遍历技巧不是很了解的同学，可以复习一下上一篇文章：LeetCode 例题精讲 | 12 岛屿问题：网格结构中的 DFS。

上一篇文章讲过了网格结构 DFS 遍历，这篇文章正好讲解一下网格结构的 BFS 遍历。要解最短路径问题，我们首先要写出层序遍历的代码，仿照上面的二叉树层序遍历代码，类似地可以写出网格层序遍历：

```java
// 网格结构的层序遍历
// 从格子 (i, j) 开始遍历
void bfs(int[][] grid, int i, int j) {
    Queue<int[]> queue = new ArrayDeque<>();
    queue.add(new int[]{r, c});
    while (!queue.isEmpty()) {
        int n = queue.size();
        for (int i = 0; i < n; i++) { 
            int[] node = queue.poll();
            int r = node[0];
            int c = node[1];
            if (r-1 >= 0 && grid[r-1][c] == 0) {
                grid[r-1][c] = 2;
                queue.add(new int[]{r-1, c});
            }
            if (r+1 < N && grid[r+1][c] == 0) {
                grid[r+1][c] = 2;
                queue.add(new int[]{r+1, c});
            }
            if (c-1 >= 0 && grid[r][c-1] == 0) {
                grid[r][c-1] = 2;
                queue.add(new int[]{r, c-1});
            }
            if (c+1 < N && grid[r][c+1] == 0) {
                grid[r][c+1] = 2;
                queue.add(new int[]{r, c+1});
            }
        }
    }
}

作者：nettee
链接：https://leetcode.cn/problems/binary-tree-level-order-traversal/solutions/244853/bfs-de-shi-yong-chang-jing-zong-jie-ceng-xu-bian-l/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

