

```
力扣208 

力扣1804 
```



Trie 树就是 [多叉树结构](https://labuladong.online/zh/algo/data-structure-basic/n-ary-tree-traverse-basic/) 的延伸，是一种针对字符串进行特殊优化的数据结构。

q。

本文仅是 Trie 树（也叫做字典树、前缀树）的原理介绍，[动手实现 TrieMap/TrieSet](https://labuladong.online/zh/algo/data-structure/trie-implement/) 我放到了[二叉树系列习题章节](https://labuladong.online/zh/algo/intro/binary-tree-practice/) 后面的数据结构设计章节。理由和上篇 [TreeMap/TreeSet 原理](https://labuladong.online/zh/algo/data-structure-basic/tree-map-basic/) 相同，在基础知识章节我不准备讲解这种复杂结构的具体实现，初学者也没必要在这个阶段理解 Trie 树的代码实现



### 实现TrieMap 和 TrieSet 

先来梳理一下我们已经实现过的 Map/Set 类型：

- 标准的 [哈希表 `HashMap`](https://labuladong.online/zh/algo/data-structure-basic/hashmap-basic/)，底层借助一个哈希函数把键值对存在 `table` 数组中，有两种解决哈希冲突的方法。它的特点是快，即基本的增删查改操作时间复杂度都是 O(1)*O*(1)。[哈希集合 `HashSet`](https://labuladong.online/zh/algo/data-structure-basic/hash-set/) 是 `HashMap` 的简单封装。
- [哈希链表 `LinkedHashMap`](https://labuladong.online/zh/algo/data-structure-basic/hashtable-with-linked-list/)，是 [双链表结构](https://labuladong.online/zh/algo/data-structure-basic/linkedlist-basic/) 对标准哈希表的加强。它继承了哈希表的操作复杂度，并且可以让哈希表中的所有键保持「插入顺序」。`LinkedHashSet` 是 `LinkedHashMap` 的简单封装。
- [哈希数组 `ArrayHashMap`](https://labuladong.online/zh/algo/data-structure-basic/hashtable-with-array/)，是 [数组结构](https://labuladong.online/zh/algo/data-structure-basic/array-basic/) 对标准哈希表的加强。它继承了哈希表的操作复杂度，并且提供了一个额外的 `randomKey` 函数，可以在 O(1)*O*(1) 的时间返回一个随机键。`ArrayHashSet` 是 `ArrayHashMap` 的简单封装。
- [`TreeMap` 映射](https://labuladong.online/zh/algo/data-structure-basic/tree-map-basic/)，底层是一棵二叉搜索树（编程语言标准库一般使用经过改良的自平衡 [红黑树](https://labuladong.online/zh/algo/data-structure-basic/rbtree-basic/)），基本增删查改操作复杂度是 O(logN)*O*(*l**o**g**N*)，它的特点是可以动态维护键值对的大小关系，有很多额外的 API 操作键值对。`TreeSet` 集合是 `TreeMap` 映射的简单封装。

`TrieSet` 也是 `TrieMap` 的简单封装，所以下面我们聚焦 `TrieMap` 的实现原理即可。



### Trie树的主要应用

**Trie 树是一种针对字符串有特殊优化的数据结构**，这也许它又被叫做字典树的原因。Trie 树针对字符串的处理有若干优势

#### 节约存储空间

用 `HashMap` 对比吧，比如说这样存储几个键值对：

```java
Map<String, Integer> map = new HashMap<>();
map.put("apple", 1);
map.put("app", 2);
map.put("appl", 3);
```

回想哈希表的实现原理，键值对会被存到 `table` 数组中，也就是说它真的创建 `"apple"`、`"app"`、`"appl"` 这三个字符串，占用了 12 个字符的内存空间。

但是注意，这三个字符串拥有共同的前缀，`"app"` 这个前缀被重复存储了三次，`"l"` 也被重复存储了两次。

如果换成 TrieMap 来存储

```java
// Trie 树的键类型固定为 String 类型，值类型可以是泛型
TrieMap<Integer> map = new TrieMap<>();
map.put("apple", 1);
map.put("app", 2);
map.put("appl", 3);
```

Trie 树底层并不会重复存储公共前缀，所以只需要 `"apple"` 这 5 个字符的内存空间来存储键。

这个例子数据量很小，你感觉重复存储几次没啥大不了，但如果键非常多、非常长，且存在大量公共前缀（现实中确实经常有这种情况，比如证件号），那么 Trie 树就能节约大量的内存空间。



#### 可以使用通配符

```java
// 匹配 "t.a." 的键有 "team", "that"
System.out.println(map.keysWithPattern("t.a.")); // ["team", "that"]
```

这个功能用 HashMap 或者 TreeMap 肯定是做不到的



#### 可以按照字典序遍历

```java
// Trie 树的键类型固定为 String 类型，值类型可以是泛型
TrieMap<Integer> map = new TrieMap<>();

map.put("that", 1);
map.put("the", 2);
map.put("them", 3);
map.put("zip", 4);
map.put("apple", 5);

// 按照字典序遍历键
System.out.println(map.keys()); // ["apple", "that", "the", "them", "zip"]
```

这个功能 `TreeMap` 也能做到，算是和 Trie 树打了个平手，但是 HashMap 就做不到了。



### Trie树的基本结构

多叉树节点的代码实现是这样：

```java
class TreeNode{
  int val;
   TreeNode[] children;
}
```







> **Trie 树本质上就是一棵从二叉树衍生出来的多叉树**。

`TrieMap` 中的树节点 `TrieNode` 的代码实现是这样

```java
//Trie 树节点

class TrieNode<V>{
  V val = null;   
  TrieNode<V>[] children = new TrieNode[256];
}
```

`V` 是 Java 中的**泛型类型参数**。

在 `TrieMap<V>` 的设计中：

- **键（Key）** 的类型被固定为 `String`。
- **值（Value）** 的类型是不确定的，由使用者决定，所以用 `V` 来代表这个“待定的类型”。

```java
// 也可以这么写

Map<Character, TrieNode<V>> children = new HashMap<>();
```

这行代码的意思是：当前节点的所有**子节点**，不再是用固定的连续数组存储，而是存在一个哈希表（HashMap）里。

- **Key（键）是 `Character`（字符）：** 代表走向下一个节点的“路标”（比如字母 'a'、'b'、'c'，或者任何字符）。
- **Value（值）是 `TrieNode<V>`：** 代表顺着这个路标走过去之后，到达的**下一个字典树节点**。



**这里要特别注意，`TrieNode` 节点本身只存储 `val` 字段，并没有一个字段来存储字符，字符是通过子节点在父节点的 `children` 数组中的索引确定的**。

**形象理解就是，Trie 树用「树枝」存储字符串（键），用「节点」存储字符串（键）对应的数据（值）。所以我在图中把字符标在树枝，键对应的值 `val` 标在节点上**

<img src="https://labuladong.online/images/algo/trie/10.jpeg" alt="img" style="zoom:50%;" />



# Trie树代码实现

前置知识

阅读本文前，你需要先学习：

- [二叉树遍历](https://labuladong.online/zh/algo/data-structure-basic/binary-tree-traverse-basic/)
- [多叉树遍历](https://labuladong.online/zh/algo/data-structure-basic/n-ary-tree-traverse-basic/)
- [Trie 树原理](https://labuladong.online/zh/algo/data-structure-basic/trie-map-basic/)
- [100 道二叉树习题强化练习](https://labuladong.online/zh/algo/intro/binary-tree-practice/)



#### Trie树API

```java
// **** 增/改 ****

    // 在 Map 中添加 key
    public void put(String key, V val);

    // **** 删 ****

    // 删除键 key 以及对应的值
    public void remove(String key);

    // **** 查 ****

    // 搜索 key 对应的值，不存在则返回 null
    // get("the") -> 4
    // get("tha") -> null
    public V get(String key);

    // 判断 key 是否存在在 Map 中
    // containsKey("tea") -> false
    // containsKey("team") -> true
    public boolean containsKey(String key);
```



#### 力扣208. 实现 Trie (前缀树) - 🔥

- 所有字典树题目的母题。面试官让你“手写前缀树”就是这道题。必须做到无错、快速地默写出来。
- 

#### **LeetCode 211. 添加与搜索单词 - 结构设计** 

- 这题在标准 Trie 的基础上引入了通配符 `.`（可以匹配任意字符）。你需要用到 **Trie + DFS（深度优先搜索）**。后端面试中，这种带模糊匹配的字典树非常高频。



### 进阶题

#### **LeetCode 677. 键值映射 (Map Sum Pairs)** `中等`

- **推荐理由：** 这道题就是你之前问的 **“带泛型/Value 的字典树”** 的简化版。它要求输入一个前缀，返回所有以此前缀开头的键的值之和。你只需要在 `TrieNode` 里加一个 `int val` 属性即可搞定。

#### **LeetCode 648. 单词替换** `中等`

- **推荐理由：** 非常贴近后端实际业务（类似于搜索引擎的词干提取/前缀匹配）。给你一个词根词典和一个句子，把句子中的单词替换成最短的词根。用 Trie 查找前缀非常高效。

### 终极挑战题（冲刺大厂/核心部门）

#### **LeetCode 212. 单词搜索 II** `困难` 🔥

- **推荐理由：** 字节跳动、微软等大厂非常爱考的硬核题。它是“二维网格矩阵搜索”与“字典树”的完美结合。如果不把单词放进 Trie 里进行**剪枝**，普通的 DFS 搜索绝对会超时。
- 给你一个字符网格和一个单词字典，找出网格里能拼出哪些单词。普通的 DFS 会超时（时间复杂度爆炸），你必须**把字典先建造成一棵 Trie 树**，然后在网格 DFS 的每一步里去 Trie 树上比对，如果不匹配直接“剪枝”（放弃这条路）。

#### **LeetCode 421. 数组中两个数的最大异或值** `中等 / 困难`

- **推荐理由：** 极其惊艳的一道题。它打破了“Trie 只能处理字符串”的固有印象，利用“0-1 字典树”（把整数的二进制位 0 和 1 视作字符）来解决位运算问题。能做出来这个，说明你对 Trie 的底层本质理解透彻了。





面试官往往不会直接说“请用字典树解决”，而是包装成系统设计或业务场景：

1. **敏感词/黑名单过滤系统：** “有 100 万个敏感词短语，高并发的聊天室收到一条用户发言，怎么在几毫秒内判断并高亮里面所有的敏感词？”
   - *答题核心：* 使用 Trie 树存储敏感词，或者基于 Trie 进化的 **AC 自动机**。
2. **搜索框自动补全（Autocomplete）：** “当用户在输入框打出 `app` 时，如何以最快速度联想出 `apple`, `application` 等前缀匹配的词？”
   - *答题核心：* Trie 树的 `startsWith` 遍历。  
3. **路由与权限匹配：** “后端 API 网关需要对 URL 进行前缀匹配（如 `/api/v1/user/*`），如何高效实现？”
   - *答题核心：* 将 URL 按 `/` 分割，每一层作为一个节点，构建一棵多叉树（其实就是我们之前讨论的用 `Map<String, TrieNode>` 实现的变种字典树）。