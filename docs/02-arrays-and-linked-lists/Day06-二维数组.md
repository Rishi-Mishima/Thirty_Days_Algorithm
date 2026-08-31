# 二维数组的花式遍历技巧



### 相关力扣题

|                           LeetCode                           |                             力扣                             | 难度 |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :--: |
| [48. Rotate Image](https://leetcode.com/problems/rotate-image/) |  [48. 旋转图像](https://leetcode.cn/problems/rotate-image/)  |  🟡   |
| [151. Reverse Words in a String](https://leetcode.com/problems/reverse-words-in-a-string/) | [151. 反转字符串中的单词](https://leetcode.cn/problems/reverse-words-in-a-string/) |  🟡   |
| [61. Rotate List](https://leetcode.com/problems/rotate-list/) |  [61. 旋转链表](https://leetcode.cn/problems/rotate-list/)   |  🟡   |
| [54. Spiral Matrix](https://leetcode.com/problems/spiral-matrix/) | [54. 螺旋矩阵](https://leetcode.cn/problems/spiral-matrix/)  |  🟡   |
| [59. Spiral Matrix II](https://leetcode.com/problems/spiral-matrix-ii/) | [59. 螺旋矩阵 II](https://leetcode.cn/problems/spiral-matrix-ii/) |  🟡   |
| [LCR 146. 螺旋遍历二维数组](https://leetcode.com/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/) | [LCR 146. 螺旋遍历二维数组](https://leetcode.cn/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/) |  🟢   |

### 顺逆时针旋转矩阵

#### 力扣48. 旋转图像

对二维数组进行旋转是常见的笔试题，力扣第 48 题「[旋转图像](https://leetcode.cn/problems/rotate-image/)」就是很经典的一道：

- 将一个二维矩阵顺时针旋转 90 度，**难点在于要「原地」修改**

<img src="https://labuladong.online/images/algo/2d-array/1.png" alt="img" style="zoom:33%;" />



相似类型题目

#### 谷歌 反转字符串 - 力扣第 151 题「[颠倒字符串中的单词](https://leetcode.cn/problems/reverse-words-in-a-string/)」

- 给你一个包含若干单词和空格的字符串 `s`，请你写一个算法，**原地**反转所有单词的顺序

```java
s = "hello world labuladong"
  
  ---> s = "labuladong world hello"
```

常规的方式是把 `s` 按空格 `split` 成若干单词，然后 `reverse` 这些单词的顺序，最后把这些单词 `join` 成句子。但这种方式使用了额外的空间，并不是「原地反转」单词

**正确的做法是，先将整个字符串 `s` 反转**：

```java
s = "gnodalubal dlrow olleh"
```

**然后将每个单词分别反转**：

```java
s = "labuladong world hello"
```

这样，就实现了原地反转所有单词顺序的目的。力扣第 151 题「[颠倒字符串中的单词](https://leetcode.cn/problems/reverse-words-in-a-string/)」就是类似的问题，你可以顺便去做一下。



#### 力扣第 61 题「[旋转链表](https://leetcode.cn/problems/rotate-list/)」

给你一个单链表，让你旋转链表，将链表每个节点向右移动 `k` 个位置。

比如说输入单链表 `1 -> 2 -> 3 -> 4 -> 5`，`k = 2`，你的算法需要返回 `4 -> 5 -> 1 -> 2 -> 3`，即将链表每个节点向右移动 2 个位置。

- 其实就是将链表的后 `k` 个节点移动到链表的头部
- 把后 `k` 个节点移动到链表的头部，其实就是让你把链表的前 `n - k` 个节点和后 `k` 个节点原地翻转
- 只需要先将整个链表反转，然后将前 `k` 个节点和后 `n - k` 个节点分别反转

细节: 

- 比如这个 `k` 可能大于链表的长度，那么你需要先求出链表的长度 `n`，然后取模 `k = k % n`，这样 `k` 就不会大于链表的长度，且最后得到的结果也是正确的。



回到61 题矩阵反转

**我们可以先将 `n x n` 矩阵 `matrix` 按照左上到右下的对角线进行镜像对称**：

<img src="https://labuladong.online/images/algo/2d-array/2.jpeg" alt="img" style="zoom:33%;" />

**然后再对矩阵的每一行进行反转**：

<img src="https://labuladong.online/images/algo/2d-array/3.jpeg" alt="img" style="zoom:33%;" />

**发现结果就是 `matrix` 顺时针旋转 90 度的结果**：

<img src="https://labuladong.online/images/algo/2d-array/4.jpeg" alt="img" style="zoom:33%;" />

##### 答案

```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        // 先沿对角线反转二维矩阵
        for (int i = 0; i < n; i++) {
            for (int j = i; j < n; j++) {
                // swap(matrix[i][j], matrix[j][i]);
                int temp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = temp;
            }
        }
        // 然后反转二维矩阵的每一行
        for (int[] row : matrix) {
            reverse(row);
        }
    }

    // 反转一维数组
    void reverse(int[] arr) {
        int i = 0, j = arr.length - 1;
        while (j > i) {
            // swap(arr[i], arr[j]);
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
            i++;
            j--;
        }
    }
}
```



#### 矩阵逆时针旋转

思路是类似的，只要通过另一条对角线镜像对称矩阵，然后再反转每一行，就得到了逆时针旋转矩阵的结果：

<img src="https://labuladong.online/images/algo/2d-array/5.jpeg" alt="img" style="zoom:33%;" />

```java
class Solution {

    // 将二维矩阵原地逆时针旋转 90 度
    public void rotate2(int[][] matrix) {
        int n = matrix.length;
        // 沿左下到右上的对角线镜像对称二维矩阵
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n - i; j++) {
                // swap(matrix[i][j], matrix[n-j-1][n-i-1])
                int temp = matrix[i][j];
                matrix[i][j] = matrix[n - j - 1][n - i - 1];
                matrix[n - j - 1][n - i - 1] = temp;
            }
        }
        // 然后反转二维矩阵的每一行
        for (int[] row : matrix) {
            reverse(row);
        }
    }

    void reverse(int[] arr) {
        // 见上文
    }
}
```



## 矩阵的螺旋遍历

#### 力扣54. 「[螺旋矩阵](https://leetcode.cn/problems/spiral-matrix/)」

**解题的核心思路是按照右、下、左、上的顺序遍历数组，并使用四个变量圈定未遍历元素的边界**：

<img src="https://labuladong.online/images/algo/2d-array/6.png" alt="img" style="zoom:33%;" />

随着螺旋遍历，相应的边界会收缩，直到螺旋遍历完整个数组：

<img src="https://labuladong.online/images/algo/2d-array/7.png" alt="img" style="zoom:33%;" />

答案

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length;
        int upper_bound = 0, lower_bound = m - 1;
        int left_bound = 0, right_bound = n - 1;
        List<Integer> res = new LinkedList<>();
        // res.size() == m * n 则遍历完整个数组
        while (res.size() < m * n) {
            if (upper_bound <= lower_bound) {
                // 在顶部从左向右遍历
                for (int j = left_bound; j <= right_bound; j++) {
                    res.add(matrix[upper_bound][j]);
                }
                // 上边界下移
                upper_bound++;
            }
            
            if (left_bound <= right_bound) {
                // 在右侧从上向下遍历
                for (int i = upper_bound; i <= lower_bound; i++) {
                    res.add(matrix[i][right_bound]);
                }
                // 右边界左移
                right_bound--;
            }
            
            if (upper_bound <= lower_bound) {
                // 在底部从右向左遍历
                for (int j = right_bound; j >= left_bound; j--) {
                    res.add(matrix[lower_bound][j]);
                }
                // 下边界上移
                lower_bound--;
            }
            
            if (left_bound <= right_bound) {
                // 在左侧从下向上遍历
                for (int i = lower_bound; i >= upper_bound; i--) {
                    res.add(matrix[i][left_bound]);
                }
                // 左边界右移
                left_bound++;
            }
        }
        return res;
    }
}
```



#### 力扣59. 「[螺旋矩阵 II](https://leetcode.cn/problems/spiral-matrix-ii/)」

是反过来，让你按照螺旋的顺序生成矩阵

答案

```java
class Solution {
    public int[][] generateMatrix(int n) {
        int[][] matrix = new int[n][n];
        int upper_bound = 0, lower_bound = n - 1;
        int left_bound = 0, right_bound = n - 1;
        // 需要填入矩阵的数字
        int num = 1;
        
        while (num <= n * n) {
            if (upper_bound <= lower_bound) {
                // 在顶部从左向右遍历
                for (int j = left_bound; j <= right_bound; j++) {
                    matrix[upper_bound][j] = num++;
                }
                // 上边界下移
                upper_bound++;
            }
            
            if (left_bound <= right_bound) {
                // 在右侧从上向下遍历
                for (int i = upper_bound; i <= lower_bound; i++) {
                    matrix[i][right_bound] = num++;
                }
                // 右边界左移
                right_bound--;
            }
            
            if (upper_bound <= lower_bound) {
                // 在底部从右向左遍历
                for (int j = right_bound; j >= left_bound; j--) {
                    matrix[lower_bound][j] = num++;
                }
                // 下边界上移
                lower_bound--;
            }
            
            if (left_bound <= right_bound) {
                // 在左侧从下向上遍历
                for (int i = lower_bound; i >= upper_bound; i--) {
                    matrix[i][left_bound] = num++;
                }
                // 左边界右移
                left_bound++;
            }
        }
        return matrix;
    }
}
```

