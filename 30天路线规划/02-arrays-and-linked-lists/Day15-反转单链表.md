|                           LeetCode                           |                             力扣                             | 难度 |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :--: |
| [206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/) | [206. 反转链表](https://leetcode.cn/problems/reverse-linked-list/) |      |
| [92. Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii/) | [92. 反转链表 II](https://leetcode.cn/problems/reverse-linked-list-ii/) |      |
| [24. Swap Nodes in Pairs](https://leetcode.com/problems/swap-nodes-in-pairs/) | [24. 两两交换链表中的节点](https://leetcode.cn/problems/swap-nodes-in-pairs/) |      |
| [25. Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/) | [25. K 个一组翻转链表](https://leetcode.cn/problems/reverse-nodes-in-k-group/) |      |

# 单链表反转

单链表反转

- 递归解法
- 迭代解法

反转单链表中的一部分 ------> k 个一组反转链表





### 反转整个单链表

```java
class ListNode{
  int val; 
  ListNode next; 
  ListNode(int x ){
    val = x; 
  }
}
```



#### 力扣206 - 反转单链表

##### 迭代解法

这道题的常规做法就是迭代解法，通过操作几个指针，将链表中的每个节点的指针方向反转，没什么难点，主要是指针操作的细节问题。

```java
class Solution {
    // 反转以 head 为起点的单链表
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        // 由于单链表的结构，至少要用三个指针才能完成迭代反转
        // cur 是当前遍历的节点，pre 是 cur 的前驱结点，nxt 是 cur 的后继结点
        ListNode pre, cur, nxt;
        pre = null; cur = head; nxt = head.next;
        while (cur != null) {
            // 逐个结点反转
            cur.next = pre;
            // 更新指针位置
            pre = cur;
            cur = nxt;
            if (nxt != null) {
                nxt = nxt.next;
            }
        }
        // 返回反转后的头结点
        return pre;
    }
}
```



代码解释: 

```java
pre = cur;
cur = nxt;
```

这两行代码的作用是**将 `pre` 和 `cur` 指针向后移动一位**，为反转下一个节点做准备。

在迭代反转链表的过程中，我们需要逐个处理节点。当执行完 `cur.next = pre`（将当前节点的指针指向前一个节点）后，当前的 `cur` 节点已经处理完毕。为了处理下一个节点，我们需要：

1. `pre = cur;`：让 `pre` 指针移动到当前处理的节点位置（因为它将成为下一个节点的前驱）。
2. `cur = nxt;`：让 `cur` 指针移动到下一个待处理的节点（即之前保存的 `nxt`）。



```markdown
> 空指针异常

- 一旦出现类似 nxt.next 这种操作，就要条件反射地想到，先判断 nxt 是否为 null，否则容易出现空指针异常
```



##### 递归解法 🌟

基于二叉树

- 二叉树结构本身就是单链表的延伸，相当于是二叉链表
- 所以二叉树上的递归思维，套用到单链表上是一样的。

> **递归反转单链表的关键在于，这个问题本身是存在子问题结构的**。

那么你这个 `reverseList` 函数，只要输入一个单链表，就能给我反转对吧？那么你能不能用这个函数先来反转 `2->3->4` 这个子链表呢，然后再想办法把 `1` 接到反转后的 `4->3->2` 的最后面，是不是就完成了整个链表的反转？



```
reverseList(1->2->3->4) = reverseList(2->3->4) -> 1
```

**这就是「分解问题」的思路，通过递归函数的定义，把原问题分解成若干规模更小、结构相同的子问题，最后通过子问题的答案组装原问题的解**。

```java
class Solution {
    // 定义：输入一个单链表头结点，将该链表反转，返回新的头结点
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode last = reverseList(head.next);
        head.next.next = head;
        head.next = null;
        return last;
    }
}
```



函数定义

`reverseList(head)` 的定义是：**输入节点 `head`，将「以 `head` 为起点」的链表反转，并返回反转后的头结点。**

**递归调用**：



```java
ListNode last = reverseList(head.next);
```

这里传入 `head.next`（即节点 2）。根据函数定义，这行代码执行完后，**以 2 为起点的子链表 `2->3` 已经被反转成了 `3->2`**，并且 `last` 指向新的头结点 `3`。

<img src="https://labuladong.online/images/algo/reverse-linked-list/3.jpg" alt="img" style="zoom:33%;" />

- `head` 依然指向 `1`。
- `1` 的 `next` 依然指向 `2`。
- 但 `2` 的 `next` 已经指向了 `null`（因为 `2` 现在是子链表的尾节点），而 `3` 的 `next` 指向 `2`。



**关键操作 1：反转指针**

```java
head.next.next = head;
```

`head.next` 是节点 `2`。这行代码的意思是：让节点 `2` 的 `next` 指向 `head`（节点 `1`）。

效果：`3 -> 2 -> 1`。注意，此时 `1` 的 `next` 依然指向 `2`，形成了一个环 `1->2->1`，且 `1` 还没有断开与后面的连接。

<img src="https://labuladong.online/images/algo/reverse-linked-list/4.jpg" alt="img" style="zoom:25%;" />

> 此时形成了一个环



**关键操作 2：断开旧连接**

```java
head.next = null;
```

将 `head`（节点 `1`）的 `next` 设为 `null`。这样 `1` 就成了新的尾节点，环被打破。

<img src="https://labuladong.online/images/algo/reverse-linked-list/5.jpg" alt="img" style="zoom:25%;" />

- **返回结果**

  ```java
  return last;
  ```

  `last` 始终是原始链表的最后一个节点（现在是反转后的头结点），层层返回即可。



> 对于「分解问题」思路的递归算法，最重要的就是明确递归函数的定义。具体来说，我们的 `reverseList` 函数定义是这样的：
>
> **输入一个节点 `head`，将「以 `head` 为起点」的链表反转，并返回反转之后的头结点**。



注意点⚠️: 

1、递归函数要有 base case，也就是这句：

```java
if (head == null || head.next == null) {
    return head;
}
```

意思是如果链表为空或者只有一个节点的时候，反转结果就是它自己，直接返回即可。

2、当链表递归反转之后，新的头结点是 `last`，而之前的 `head` 变成了最后一个节点，别忘了链表的末尾要指向 null：

```java
head.next = null;
```



```
递归操作链表的效率不如迭代
值得一提的是，递归操作链表并不高效。

递归解法和迭代解法相比，时间复杂度都是 O(N)，但是迭代解法的空间复杂度是 O(1)，而递归解法需要堆栈，空间复杂度是 O(N)。

所以递归操作链表可以用来练习递归思维，但是考虑效率的话还是使用迭代算法更好。
```



### 反转链表的前N个节点

这次我们实现一个这样的函数：

```java
// 将链表的前 n 个节点反转（n <= 链表长度）
ListNode reverseN(ListNode head, int n)
```

<img src="https://labuladong.online/images/algo/reverse-linked-list/6.jpg" alt="img" style="zoom:25%;" />

比如说对于上面链表，执行 `reverseN(head, 3)`

#### 迭代解法

迭代解法应该比较好写，在之前实现的 `reverseList` 基础上稍加修改就可以了：

```java
ListNode reverseN(ListNode head, int n) {
    if (head == null || head.next == null) {
        return head;
    }
  //初始化指针
    ListNode pre, cur, nxt;
    pre = null; cur = head; nxt = head.next;
    while (n > 0) {
        cur.next = pre;   // 1. 反转指针：让当前节点指向前一个节点
    		pre = cur;        // 2. pre 前进一步
    		cur = nxt;        // 3. cur 前进一步
        	if (nxt != null) {
            nxt = nxt.next;
        }
        n--;    // 5. 计数器减一
    }
    // 此时的 cur 是第 n + 1 个节点，head 是反转后的尾结点
    head.next = cur;
    // 此时的 pre 是反转后的头结点
    return pre;
}
```

核心逻辑是在标准的全链表反转基础上，增加了两个关键处理：

1. **控制反转数量**：只循环 `n` 次。
2. **连接剩余部分**：将反转后的尾部（原 `head`）与第 `n+1` 个节点连起来。

这个循环每执行一次，就完成一个节点的指针反转。当 `n` 减到 0 时，循环结束。此时：

- `pre` 指向了**反转部分的新的头结点**（即原来的第 `n` 个节点）。
- `cur` 指向了**第 `n + 1` 个节点**（即反转部分之后的第一个节点）。
- `head` 依然是原始链表的头，但现在它已经是**反转部分的尾结点**了。

`head.next = cur`：这是最关键的一步。因为只反转了前 `n` 个，所以反转后的最后一个节点（原 `head`）必须连接到剩下的链表（从第 `n+1` 个节点开始，即 `cur`）。如果不连这一步，链表会在第 `n` 个节点处断开。

- `return pre`：返回新的头结点。



 `head.next = cur;` —— 连接断点

在循环开始前，`head` 是链表的第一个节点。在反转过程中，`head` 的指针方向被改成了指向 `null`（因为它变成了反转部分的尾巴）。

但是，我们只反转了前 `n` 个节点，后面的节点（从第 `n+1` 个开始）还在那儿等着呢。

- **`cur` 的身份**：当 `while(n > 0)` 循环结束时，`cur` 刚好移动到了**第 `n + 1` 个节点**的位置。
- **`head` 的身份**：`head` 现在是反转后的**尾结点**。

所以，`head.next = cur` 的意思就是：**让反转后的尾巴，连上后面没反转的链表头。** 如果不写这一句，链表就在第 `n` 个节点处断开了。

2. `return pre;` —— 返回新头

- **`pre` 的身份**：在循环中，`pre` 始终跟在 `cur` 前面。当循环结束，`pre` 刚好停在**第 `n` 个节点**上。
- 因为前 `n` 个节点反转了，原来的第 `n` 个节点现在就变成了**新的头结点**。



> ### 注意事项
>
> 代码开头的 `if (head == null || head.next == null)` 其实对于 `reverseN` 来说不是必须的 base case，因为即使只有一个节点，只要 `n=1`，循环也会正确执行一次并返回。但在实际工程中保留它可以防止一些边界异常。



#### 递归解法

<img src="https://labuladong.online/images/algo/reverse-linked-list/7.jpg" alt="img" style="zoom:25%;" />

递归思路和递归反转整个链表差不多，只要稍加修改即可：

```java
// 后驱节点
ListNode successor = null;

// 反转以 head 为起点的 n 个节点，返回新的头结点
ListNode reverseN(ListNode head, int n) {
    if (n == 1) {
        // 记录第 n + 1 个节点
        successor = head.next;
        return head;
    }
    // 以 head.next 为起点，需要反转前 n - 1 个节点
    ListNode last = reverseN(head.next, n - 1);

    head.next.next = head;
    // 让反转之后的 head 节点和后面的节点连起来
    head.next = successor;
    return last;
}
```

> 核心难点在于：**反转后，原来的头结点 `head` 不再是链表的末尾，它需要连接到第 `n+1` 个节点**

1. 全局变量 `successor` 的作用

```java
ListNode successor = null;
```

这是一个“后驱节点”。在反转前 `n` 个节点后，第 `n` 个节点（反转后的尾结点）需要指向第 `n+1` 个节点。这个 `successor` 就是用来记录第 `n+1` 个节点的。



2. Base Case：`n == 1`

```java
if (n == 1) {
    // 记录第 n + 1 个节点
    successor = head.next;
    return head;
}
```

- 当 `n=1` 时，意味着只需要反转 1 个节点，也就是它自己，不需要改变指针方向。
- **关键点**：此时 `head` 就是这 `n` 个节点中的最后一个。我们需要记下它的下一个节点 `head.next`，这就是后续要连接的“后驱节点”。



3. 递归过程

```
ListNode last = reverseN(head.next, n - 1);
```

- 我们假设 `reverseN` 函数能完美反转以 `head.next` 为起点的前 `n-1` 个节点。
- `last` 是反转这部分子链表后返回的新头结点。



4. 指针反转与连接

```java
head.next.next = head;
// 让反转之后的 head 节点和后面的节点连起来
head.next = successor;
return last;
```

- `head.next.next = head;`：这是标准的递归反转操作，把当前 `head` 挂到后面已经反转好的链表末尾。
- `head.next = successor;`：**这是与普通反转最大的不同**。普通反转中 `head` 变成尾结点后指向 `null`，但这里 `head` 只是局部反转的尾结点，它必须指向之前记录好的 `successor`（即第 `n+1` 个节点），从而把反转部分和未反转部分接上。
- 最后返回 `last`，即这一小段反转后的新头结点。



总结

- **普通反转**：尾结点指向 `null`。
- **反转前 N 个**：尾结点指向 `successor`（第 `n+1` 个节点）。
- **技巧**：利用全局变量或引用传递来保存“断点”后的第一个节点，确保链表不断裂。



为什么要记录successor? 

对比一下两种情况：

1. 反转整个链表

- **目标**：把 `1->2->3->null` 变成 `3->2->1->null`。
- **结果**：原来的 `head` (节点 1) 变成了尾巴。
- **操作**：在递归回溯时，直接执行 `head.next = null` 即可。因为后面没有节点了，不需要连接任何人，直接断掉就行。所以不需要记录“后驱节点”。

2. 反转前 N 个节点

- **目标**：把 `1->2->3->4->5` 的前 3 个反转，变成 `3->2->1->4->5`。
- **结果**：原来的 `head` (节点 1) 只是**局部**的尾巴，它后面还有节点 4 和 5。
- **操作**：如果不记录节点 4（即 `successor`），你就不知道 `head` 应该连向谁。如果像普通反转那样执行 `head.next = null`，链表就断成两截了：`3->2->1->null` 和 `4->5` 丢失了。

**反转全部**：尾结点的下一位是固定的 `null`，无需记录。

**反转部分**：尾结点的下一位是**动态**的第 `n+1` 个节点，必须在递归触底（`n==1`）时提前记录下来，以便回溯时进行连接。



### 反转链表的一部分(给定区间)

#### 力扣92. 反转链表II

题目输入索引区间 `[m, n]`（索引从 1 开始），仅仅反转区间中的链表元素，函数签名如下：

```java
ListNode reverseBetween(ListNode head, int m, int n)
```



#### 迭代解法

纯迭代的思路比较直接，可以先找到第 `m - 1` 个节点，然后复用之前实现的 `reverseN` 函数就行了：

```java
class Solution {
    public ListNode reverseBetween(ListNode head, int m, int n) {
        if (m == 1) {
            return reverseN(head, n);
        }
        // 找到第 m 个节点的前驱
        ListNode pre = head;
        for (int i = 1; i < m - 1; i++) {
            pre = pre.next;
        }
        // 从第 m 个节点开始反转
        pre.next = reverseN(pre.next, n - m + 1);
        return head;
    }

    ListNode reverseN(ListNode head, int n) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode pre, cur, nxt;
        pre = null; cur = head; nxt = head.next;
        while (n > 0) {
            cur.next = pre;
            pre = cur;
            cur = nxt;
            if (nxt != null) {
                nxt = nxt.next;
            }
            n--;
        }
        // 此时的 cur 是第 n + 1 个节点，head 是反转后的尾结点
        head.next = cur;
        // 此时的 pre 是反转后的头结点
        return pre;
    }
}
```



- **`pre.next`**：此时 `pre` 是第 `m-1` 个节点，所以 `pre.next` 就是第 `m` 个节点，也就是**待反转子链表的头节点**。
- **`n - m + 1`**：这是待反转节点的**个数**。例如反转第 2 到第 4 个节点，个数就是 `4 - 2 + 1 = 3` 个

- **`reverseN(pre.next, n - m + 1)`**：调用之前实现的“反转前 N 个节点”函数。它会将这 `k` 个节点反转，并返回反转后的新头节点。
- **`pre.next = ...`**：将第 `m-1` 个节点的 `next` 指针指向反转后的新头节点，从而把反转好的部分接回原链表。
- **`return head`**：因为头结点 `head` 本身没有变（除非 `m=1`，但 `m=1` 的情况在之前的 `if (m == 1)` 分支中已经处理了），所以直接返回 `head` 即可。



为什么从i = 1 开始

- 只是为了符合人类计数习惯

从 0 开始完全可以，只要把条件改成 `i < m - 2` 即可



#### 递归解法

纯递归解法，依然是找到第 `m - 1` 个节点，然后复用之前实现的 `reverseN` 函数就行了。

关键是，如何通过递归的方式找到第 `m - 1` 个节点呢？

如果我们把 `head` 的索引视为 1，那么我们是想从第 `m` 个元素开始反转对吧；如果把 `head.next` 的索引视为 1 呢？那么相对于 `head.next`，反转的区间应该是从第 `m - 1` 个元素开始的；那么对于 `head.next.next` 呢……

```java
class Solution {
    public ListNode reverseBetween(ListNode head, int m, int n) {
        // base case
      // base case：当 m == 1 时，说明当前 head 就是反转区间的第一个节点
    // 此时问题转化为：反转以 head 为起点的前 n 个节点
        if (m == 1) {
            return reverseN(head, n);
        }
      
        // 前进到反转的起点触发 base case
      // 如果 m > 1，说明反转起点还在后面
    // 我们将 head.next 视为新的头结点，那么反转区间相对于新头结点的起始位置就变成了 m - 1
    // 同时，结束位置也相应减 1，变为 n - 1
        head.next = reverseBetween(head.next, m - 1, n - 1);
        return head;
    }

    // 后驱节点
    ListNode successor = null;

    // 反转以 head 为起点的 n 个节点，返回新的头结点
    ListNode reverseN(ListNode head, int n) {
        if (n == 1) {
            // 记录第 n + 1 个节点
            successor = head.next;
            return head;
        }
        ListNode last = reverseN(head.next, n - 1);

        head.next.next = head;
        head.next = successor;
        return last;
    }
}
```

**逻辑推导：**

- 假设链表是 `1->2->3->4->5`，要反转 `[2, 4]`（即 `m=2, n=4`）。
- 第一次调用：`m=2`，不满足 base case。执行 `head.next = reverseBetween(2->3->4->5, 1, 3)`。
- 第二次调用：`m=1`，满足 base case。执行 `reverseN(2->3->4->5, 3)`。
- 这就成功将「反转区间 `[m, n]`」转化为了「反转前 `n` 个节点」。



2. `reverseN`：反转前 N 个节点

这是递归反转的核心。它与反转整个链表的区别在于：**反转一部分后，需要将反转后的尾部连接到未反转的部分**。

```java
// 后驱节点：记录第 n + 1 个节点，用于连接反转后的尾部
ListNode successor = null;

ListNode reverseN(ListNode head, int n) {
    // base case：只反转 1 个节点时，它自己就是反转后的头结点
    // 此时需要记录它的下一个节点（即第 n+1 个节点），作为后驱
    if (n == 1) {
        successor = head.next;
        return head;
    }
    
    // 递归反转后面的 n-1 个节点
    // last 是反转这部分子链表后返回的新头结点
    ListNode last = reverseN(head.next, n - 1);

    // 下面两步是标准的递归反转指针操作
    // 让 head 的下一个节点的 next 指向 head（即反转指针方向）
    head.next.next = head;
    
    // 关键区别：不再将 head.next 设为 null，而是连上之前记录的 successor
    // 这样就把反转部分的尾部和剩余部分连起来了
    head.next = successor;
    
    return last;
}
```



总结

- **[reverseBetween](https://labuladong.online/zh/algo/data-structure/reverse-linked-list-recursion/#反转链表的一部分)** 负责「走位」，通过递归递减 `m` 找到起点。
- **[reverseN](https://labuladong.online/zh/algo/data-structure/reverse-linked-list-recursion/#反转链表前-n-个节点)** 负责「干活」，反转前 N 个节点并妥善处理与后续链表的连接。



### K 个一组反转链表

#### 力扣25. K个一组反转链表

**这个问题具有递归性质**。

比如说我们对这个链表调用 `reverseKGroup(head, 2)`，即以 2 个节点为一组反转链表

如果我设法把前 2 个节点反转，那么后面的那些节点怎么处理？后面的这些节点也是一条链表，而且规模（长度）比原来这条链表小，这就叫规模更小，结构相同的子问题。

我们可以把原先的 `head` 指针移动到后面这一段链表的开头，然后继续递归调用 `reverseKGroup(head, 2)`

发现了递归性质，就可以得到大致的算法流程：

**1、先反转以 `head` 开头的 `k` 个元素**。这里可以复用前面实现的 `reverseN` 函数。

<img src="https://labuladong.online/images/algo/kgroup/3.jpg" alt="img" style="zoom:25%;" />

**将第 `k + 1` 个元素作为 `head` 递归调用 `reverseKGroup` 函数**。

<img src="https://labuladong.online/images/algo/kgroup/4.jpg" alt="img" style="zoom:25%;" />

**将上述两个过程的结果连接起来**。

```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        if (head == null) return null;
        // 区间 [a, b) 包含 k 个待反转元素
        ListNode a, b;
        a = b = head;
        for (int i = 0; i < k; i++) {
            // 不足 k 个，不需要反转了
            if (b == null) return head;
            b = b.next;
        }
        // 反转前 k 个元素
        ListNode newHead = reverseN(a, k);
        // 此时 b 指向下一组待反转的头结点
        // 递归反转后续链表并连接起来
        a.next = reverseKGroup(b, k);
        return newHead;
    }

    // 上文实现的反转前 N 个节点的函数
    ListNode reverseN(ListNode head, int n) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode pre, cur, nxt;
        pre = null; cur = head; nxt = head.next;
        while (n > 0) {
            cur.next = pre;
            pre = cur;
            cur = nxt;
            if (nxt != null) {
                nxt = nxt.next;
            }
            n--;
        }
        head.next = cur;
        return pre;
    }
}
```

- **确定范围 `[a, b)`**：用两个指针 `a` 和 `b`。`a` 指向当前组的头，`b` 向前移动 `k` 步。如果中途 `b` 变成 `null`，说明剩余节点不足 `k` 个，直接返回 `head`（保持原序）。
- **反转前 K 个**：调用 `reverseN(a, k)` 反转区间 `[a, b)` 内的节点。注意，`reverseN` 返回的是反转后的新头结点 `newHead`。
- **递归连接**：反转后，原来的头结点 `a` 变成了这一组的尾结点。我们将 `a.next` 指向递归处理下一组的结果 `reverseKGroup(b, k)`。
- **返回新头**：整个链表的新头是 `newHead`。

<img src="https://labuladong.online/images/algo/kgroup/6.jpg" alt="图解" style="zoom:25%;" />





##### 对比反转单链表和反转单链表的前N个节点

1. **反转整个链表** (`reverseList`)：
   - 原来的头结点 `head` 反转后会变成**整个链表的最后一个节点**。
   - 因此，它的 `next` 必须指向 `null`。
   - 代码中体现为：`head.next = null;`
   - 这里不需要额外的 `successor` 变量，因为后驱就是 `null`。
2. **反转前 N 个节点** (`reverseN`)：
   - 原来的头结点 `head` 反转后会变成**这 N 个节点中的最后一个**，但它后面可能还有第 `N+1`、`N+2`... 个节点。
   - 因此，它的 `next` 不能指向 `null`，而必须指向**第 `N+1` 个节点**。
   - 这个第 `N+1` 个节点就是 `successor`。

需要记录successor

迭代解法**其实也记录了**，只是没有用一个名为 `successor` 的全局变量或额外变量来显式存储，而是直接复用了循环结束时的指针状态。

1. 递归解法 (`reverseN`)

递归是“自顶向下”深入，再“自底向上”返回。

- 当递归到 base case (`n == 1`) 时，我们处于第 N 个节点。
- 此时我们需要知道第 `N+1` 个节点是谁，才能把它存起来，等递归回溯回去时，让原来的头结点连上它。
- 因为递归回溯过程中，`head` 是最初传入的那个头结点，它无法直接访问到第 `N+1` 个节点（除非一路传参或者用全局变量），所以必须用 `successor` **提前记录**下来。

2. 迭代解法 (`reverseN`)

迭代是“从头到尾”线性扫描。

- 我们使用 `cur`, `pre`, `nxt` 三个指针遍历。
- 当循环执行完 `n` 次后：
  - `pre` 指向反转后的新头结点（即原来的第 N 个节点）。
  - `cur` 指向**第 `N+1` 个节点**（即后驱节点）。
  - `head` 依然指向原来的头结点（现在是反转部分的尾结点）。
- 此时，`cur` 指针本身就指着第 `N+1` 个节点！我们不需要额外找个地方存它，直接用 `cur` 就行了。

### 总结

- **递归**：因为控制流会跳回最初的调用点，中间的指针状态丢失，所以需要用 `successor` 变量**显式保存**第 `N+1` 个节点。
- **迭代**：循环结束时，指针 `cur` **天然就停留**在第 `N+1` 个节点上，直接用它即可，无需额外变量。