# 二分查找

读完本文，你不仅学会了算法套路，还可以顺便解决如下题目：

|                           LeetCode                           |                             力扣                             | 难度 |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :--: |
| [704. Binary Search](https://leetcode.com/problems/binary-search/) | [704. 二分查找](https://leetcode.cn/problems/binary-search/) |      |
| [34. Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/) | [34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/) |      |
| [LCR 172. 统计目标成绩的出现次数](https://leetcode.com/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/) | [LCR 172. 统计目标成绩的出现次数](https://leetcode.cn/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/) |      |

### 框架

二分搜索主要有两种主流写法

- 分别是「两端都闭」的写法和「左闭右开」的写法。
- **本文介绍「两端都闭」的写法**，因为这种写法更容易记忆和统一。「左闭右开」的写法会在 [二分搜索的左闭右开写法](https://labuladong.online/zh/algo/essential-technique/binary-search-left-open/) 拓展。

```java
int binarySearch(int[] nums, int target) {
    int left = 0, right = ...;

    while(...) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            ...
        } else if (nums[mid] < target) {
            left = ...
        } else if (nums[mid] > target) {
            right = ...
        }
    }
    return ...;
}
```

其中 `...` 标记的部分，就是可能出现细节问题的地方，当你见到一个二分查找的代码时，首先注意这几个地方，后面用实例分析这些地方能有什么样的变化。

想写对二分查找的一个技巧是：不要出现 else，而是把所有情况用 else if 写清楚，这样可以清楚地展现所有细节。当你能够理清所有细节之后，可以自行简化。

**另外提前说明一下，计算 `mid` 时需要防止溢出**，代码中 `left + (right - left) / 2` 就和 `(left + right) / 2` 的结果相同，但是有效防止了 `left` 和 `right` 太大，直接相加导致整型溢出的情况。



### 搜索区间

比如说，如果我们把 `right` 初始化为 `nums.length - 1`，即最后一个元素的索引，那么搜索区间就是两端都闭的 `[left, right]`，这个索引区间就是每次进行搜索的范围。

如果 `right` 初始化为 `nums.length`，即最后一个元素的索引 + 1，那么搜索区间就是左闭右开的 `[left, right)`。

**二分搜索的本质，其实就是利用 `while` 循环调整 `left` 和 `right` 指针，不断收缩搜索区间，最终锁定目标值所在的那个索引**。

搜索区间的开闭不同，对应的写法就不同。对于一个正确的二分搜索算法，我们需要确保：

- 搜索区间为空时，应该停止搜索，否则算法就会死循环。
- 搜索的过程中，不能漏掉元素，否则就会得到错误的答案。

记住这两个原则，下面我们来使用「**两端都闭**」的二分搜索写法，探讨二分搜索的三种场景：寻找一个数、寻找左侧边界、寻找右侧边界。



### 寻找一个数

这个场景是最简单的，搜索一个数，如果存在返回其索引，否则返回 -1。

```java
class Solution {
    // 标准的二分搜索框架，搜索目标元素的索引，若不存在则返回 -1
    public int search(int[] nums, int target) {
        int left = 0;
        // 注意
        int right = nums.length - 1;

        while(left <= right) {
            int mid = left + (right - left) / 2;
            if(nums[mid] == target) {
                return mid;   
            } else if (nums[mid] < target) {
                // 注意
                left = mid + 1;
            } else if (nums[mid] > target) {
                // 注意
                right = mid - 1;
            }
        }
        return -1;
    }
}
```



这段代码可以解决力扣第 704 题「[二分查找](https://leetcode.cn/problems/binary-search/)」，我们深入探讨一下其中的细节。

### 细节

#### 为什么 while 循环的条件是 `<=` 而不是 `<`？

对于两端都闭的搜索区间，`<=` 不会漏掉元素。

`while(left <= right)` 的终止条件是 `left == right + 1`，此时的搜索区间是 `[right + 1, right]`，没有元素既大于等于 `right+1` 又小于等于 `right`，所以搜索区间为空，while 循环终止是正确的。

如果使用 `while(left < right)`，终止条件是 `left == right`，写成区间的形式就是 `[left, left]`，**这时候搜索区间还有一个元素**，但 while 循环终止了，就会漏掉这个元素，如果目标值恰好是这个元素，算法就会误以为目标值不存在。

从这里也可以看出来二分搜索为什么难完全写对：bug 是偶发性的，可能大部分测试用例都能得到正确答案，只有特定的测试用例会出现错误。

所以必须彻底理解搜索区间，才能写出完全正确的代码



#### 为什么是 `left = mid + 1`，`right = mid - 1`？

因为 `mid` 已经搜索过，应该从搜索区间中去除。

因为我们的搜索区间是两端都闭的 `[left, right]`。当我们发现索引 `mid` 不是要找的 `target` 时，搜索区间应该收缩为 `[left, mid-1]` 或者区间 `[mid+1, right]`。

#### 此算法有什么缺陷？

比如说给你有序数组 `nums = [1,2,2,2,3]`，`target` 为 2，此算法返回的索引是 2，没错。但是如果我想得到 `target` 的左侧边界，即索引 1，或者我想得到 `target` 的右侧边界，即索引 3，这样的话此算法是无法处理的。

你也许会说，找到一个 `target`，然后向左或向右线性搜索不行吗？可以，但是这样难以保证二分查找对数级的复杂度了。

我们后续的算法就来讨论这两种寻找边界的算法，这也是实际算法题中比较常见的场景。



### 寻找左边界

```java
int left_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    // 搜索区间为 [left, right]
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            // 搜索区间变为 [mid+1, right]
            left = mid + 1;
        } else if (nums[mid] > target) {
            // 搜索区间变为 [left, mid-1]
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 收缩右侧边界
            right = mid - 1;
        }
    }
    return left;
}
```



#### 为什么该算法能够搜索左侧边界？

关键在于对 `nums[mid] == target` 这个条件的处理：

```java
if (nums[mid] == target) {
    // 收缩右侧边界
    right = mid - 1;
}
```

找到 target 时不要立即返回，而是收缩右边界 `right = mid - 1`，在区间 `[left, mid-1]` 中继续搜索，即不断向左收缩，达到锁定左侧边界的目的



#### 如果 `target` 不存在怎么办？

如果 `target` 不存在，`left_bound` 返回的索引是「**大于 `target` 的最小索引**」。

这个结论不用死记，举个例子就明白了：`nums = [2,3,5,7], target = 4`，`left_bound` 返回值是 2，因为元素 5 是大于 4 的最小元素。

如果你想在 `target` 不存在时返回 -1，在返回时额外判断一下 `nums[left]` 是否等于 `target` 就行了：

```java
// 如果越界，target 肯定不存在，返回 -1
if (left < 0 || left >= nums.length) {
    return -1;
}
// 判断一下 nums[left] 是不是 target
if (nums[left] != target) {
    return -1;
}
// nums[left] == target，目标索引为 left
return left;
```



### 寻找右边界

```java
int right_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 这里改成收缩左侧边界即可
            left = mid + 1;
        }
    }
    // 最后改成返回 right
    return right;
}
```



#### 为什么该算法能够搜索右侧边界？

关键在于对 `nums[mid] == target` 这个条件的处理：

```java
if (nums[mid] == target) {
    // 收缩左侧边界
    left = mid + 1;
}
```

找到 target 时不要立即返回，而是收缩左边界 `left = mid + 1`，在区间 `[mid+1, right]` 中继续搜索，即不断向右收缩，达到锁定右侧边界的目的。



#### 如果 `target` 不存在怎么办？

如果 `target` 不存在，`right_bound` 返回的索引是「**小于 `target` 的最大索引**」。

比如 `nums = [2,3,5,7], target = 4`，`right_bound` 返回值是 1，因为元素 3 是小于 4 的最大元素。

如果你想在 `target` 不存在时返回 -1，在返回时额外判断一下 `nums[right]` 是否等于 `target` 就行了：

```java
// 索引越界则说明不存在 target
if (right < 0 || right >= nums.length) {
    return -1;
}
// 目标索引不等于 target 则说明不存在 target
if (nums[right] != target) {
    return -1;
}
// nums[right] == target，目标索引为 right
return right;
```



### 统一模板

三种场景使用统一的两端都闭搜索区间 `[left, right]`，只有 `nums[mid] == target` 时的处理不同：

```java
int binary_search(int[] nums, int target) {
    int left = 0, right = nums.length - 1; 
    while(left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1; 
        } else if(nums[mid] == target) {
            // 直接返回
            return mid;
        }
    }
    // 直接返回
    return -1;
}

int left_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 别返回，锁定左侧边界
            right = mid - 1;
        }
    }
    // 判断 target 是否存在于 nums 中
    if (left < 0 || left >= nums.length) {
        return -1;
    }
    // 判断一下 nums[left] 是不是 target
    return nums[left] == target ? left : -1;
}

int right_bound(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else if (nums[mid] > target) {
            right = mid - 1;
        } else if (nums[mid] == target) {
            // 别返回，锁定右侧边界
            left = mid + 1;
        }
    }
    // 由于 while 的结束条件是 right == left - 1，且现在在求右边界
    // 所以用 right 替代 left - 1 更好记
    if (right < 0 || right >= nums.length) {
        return -1;
    }
    return nums[right] == target ? right : -1;
}
```

