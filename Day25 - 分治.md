|                           LeetCode                           |                             力扣                             | 难度 |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :--: |
| [23. Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/) | [23. 合并 K 个升序链表](https://leetcode.cn/problems/merge-k-sorted-lists/) |  🔴   |

广义的分治思想是一个宽泛的概念

- 本站教程中也经常称之为「**分解问题的思路**」。

> 分治思想就是把一个问题分解成若干个子问题，然后分别解决这些子问题，最后合并子问题的解得到原问题的解，这种思想广泛存在于递归算法中。

参考斐波那契数列

普通的二叉树算法，比如让你计算一棵二叉树总共有多少个节点：

```java
// 定义：输入一棵二叉树的根节点，返回这棵树的节点总数
int count(TreeNode root) {
    // base case
    if (root == null) {
        return 0;
    }
    // 先算出左右子树的节点个数
    int leftCount = count(root.left);
    int rightCount = count(root.right);

    // 左右子树的节点个数加上自己，就是整棵树的节点个数
    return leftCount + rightCount + 1;
}
```



### 无效的分治

没有降低时间复杂度，反而由于递归而增加了空间复杂度

**1、分治的思想是广泛存在的**，几乎所有算法都可以改写成递归分治的形式。

**2、分治思想不等于高效**。不要听到 XX 算法就觉得高大上，很多时候，改写成分治解法并不能带来什么实际的好处，甚至可能增加空间复杂度，因为递归调用需要堆栈空间。

**3、用二分的方式进行分治可以将递归树的深度从 O(n)\*O\*(\*n\*) 降低到 O(log⁡n)\*O\*(log\*n\*)，确实有优化效果**。对于上面这个元素求和的例子，无论怎么分治都不如原解法高效，但可以看出二分的分治方式是确实有助于减少递归树的高度。



### 有效的分治

这里重新探讨 [单链表双指针技巧汇总](https://labuladong.online/zh/algo/essential-technique/linked-list-skills-summary/) 中的一个问题，合并 k*k* 个有序链表：

在 [单链表双指针技巧汇总](https://labuladong.online/zh/algo/essential-technique/linked-list-skills-summary/) 中，我介绍的解法是利用 [优先级队列](https://labuladong.online/zh/algo/data-structure-basic/binary-heap-basic/) 这种数据结构对链表节点进行动态排序，这种解法的时间复杂度是 O(Nlog⁡k)*O*(*N*log*k*)，空间复杂度是 O(k)*O*(*k*)，其中 k*k* 代表链表的条数，N*N* 代表 k*k* 条链表节点的总数，

在本文中，我们不再依赖额外的数据结构，而是直接用分治算法解决这个问题，时间复杂度依然是 O(Nlog⁡k)*O*(*N*log*k*)。

首先，我们要解决合并两个有序链表的问题，也就是力扣第 21 题：

这道题也是 [单链表双指针技巧汇总](https://labuladong.online/zh/algo/essential-technique/linked-list-skills-summary/) 中的例题，标准的双指针解法，这里直接贴出解法代码

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        // 虚拟头结点
        ListNode dummy = new ListNode(-1), p = dummy;
        ListNode p1 = l1, p2 = l2;

        while (p1 != null && p2 != null) {
            // 比较 p1 和 p2 两个指针
            // 将值较小的的节点接到 p 指针
            if (p1.val > p2.val) {
                p.next = p2;
                p2 = p2.next;
            } else {
                p.next = p1;
                p1 = p1.next;
            }
            // p 指针不断前进
            p = p.next;
        }

        if (p1 != null) {
            p.next = p1;
        }

        if (p2 != null) {
            p.next = p2;
        }

        return dummy.next;
    }
}
```

这个算法使用两个指针，**把两个链表都遍历了一遍**，所以时间复杂度是 O(l1+l2)*O*(*l*1+*l*2)，l1*l*1 和 l2*l*2 分别是两个链表的长度。

下面我们来思考如何合并 k*个有序链表，先想一个暴力解吧，运用上面的这个 `mergeTwoLists` 函数把 k*k* 个链表两两合并，都合并到第一个链表上：

```java
ListNode mergeKLists(ListNode[] lists) {
    if (lists.length == 0) {
        return null;
    }
    // 把 k 个有序链表都合并到 lists[0] 上
    ListNode l0 = lists[0];
    for (int i = 1; i < lists.length; i++) {
        l0 = mergeTwoLists(l0, lists[i]);
    }
    return l0;
}

ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    // 见上文
}
```

这样肯定是能得到正确答案的，我尝试去力扣上提交 Java 代码，可以通过，但是速度非常慢，这是什么原因呢？

假设输入的 k*k* 个链表的长度分别为 l0,l1,⋯ ,lk−1*l*0,*l*1,⋯,*l**k*−1，算法会调用 k−1*k*−1 次 `mergeTwoLists` 函数，每次调用 `mergeTwoLists` 方法的时间复杂度两个链表的长度之和。

那么第一次调用 `mergeTwoLists` 方法的时间复杂度是 l0+l1*l*0+*l*1，第二次调用 `mergeTwoLists` 方法的时间复杂度是 l0+l1+l2*l*0+*l*1+*l*2，以此类推，最后一次调用 `mergeTwoLists` 方法的时间复杂度是 l0+l1+⋯+lk−1*l*0+*l*1+⋯+*l**k*−1。

综上，链表 l0*l*0 和 l1*l*1 会被遍历 k−1*k*−1 次，l2*l*2 会被遍历 k−2*k*−2 次，以此类推，最后一条链表 lk−1*l**k*−1 会被遍历 11 次。

看到冗余计算了吗？**越靠前的链表被重复遍历的次数越多，这就是这个算法低效的原因。我们只要减少这种重复，就能提高算法的效率**。

为什么会出现这种情况呢？这个场景就特别像上面的 `getSum2` 函数所面临的场景，比如我把上面实现的 `mergeKLists` 改成递归的形式：

```java
// 定义：合并 lists[start..] 为一个有序链表
ListNode mergeKLists2(ListNode[] lists, int start) {
    if (start == lists.length - 1) {
        return lists[start];
    }
    // 合并 lists[start + 1..] 为一个有序链表
    ListNode subProblem = mergeKLists2(lists, start + 1);

    // 合并 lists[start] 和 subProblem，就得到了 lists[start..] 的有序链表
    return mergeTwoLists(lists[start], subProblem);
}
```

不难发现重复的次数取决于树高，上面这个算法的递归树很不平衡，导致递归树退化成链表，树高变为 O(k)*O*(*k*)。

**如果能让递归树尽可能地平衡，就能减小树高，进而减少链表的重复遍历次数，提高算法的效率**。

如何让递归树平衡呢？就类似上面 `getSum3` 函数的思路，把链表从中间分成两部分，分别递归合并为有两个序链表，最后再将这两部分合并成一个有序链表。

请看完整的解法代码：

```java
// 用分治算法合并 k 个有序链表
class Solution {

    public ListNode mergeKLists(ListNode[] lists) {
        if (lists.length == 0) {
            return null;
        }
        return mergeKLists3(lists, 0, lists.length - 1);
    }


    // 定义：合并 lists[start..end] 为一个有序链表
    ListNode mergeKLists3(ListNode[] lists, int start, int end) {
        if (start == end) {
            return lists[start];
        }

        int mid = start + (end - start) / 2;
        // 合并左半边 lists[start..mid] 为一个有序链表
        ListNode left = mergeKLists3(lists, start, mid);

        // 合并右半边 lists[mid+1..end] 为一个有序链表
        ListNode right = mergeKLists3(lists, mid + 1, end);

        // 合并左右两个有序链表
        return mergeTwoLists(left, right);
    }


    // 双指针技巧合并两个有序链表
    // https://labuladong.online/algo/essential-technique/linked-list-skills-summary/
    ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(-1), p = dummy;
        ListNode p1 = l1, p2 = l2;

        while (p1 != null && p2 != null) {
            if (p1.val > p2.val) {
                p.next = p2;
                p2 = p2.next;
            } else {
                p.next = p1;
                p1 = p1.next;
            }
            p = p.next;
        }

        if (p1 != null) {
            p.next = p1;
        }

        if (p2 != null) {
            p.next = p2;
        }

        return dummy.next;
    }
}
```

