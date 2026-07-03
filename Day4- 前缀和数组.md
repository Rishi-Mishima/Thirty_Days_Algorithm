|                           LeetCode                           |                             力扣                             | 难度 |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :--: |
| [303. Range Sum Query - Immutable](https://leetcode.com/problems/range-sum-query-immutable/) | [303. 区域和检索 - 数组不可变](https://leetcode.cn/problems/range-sum-query-immutable/) |  🟢   |
| [304. Range Sum Query 2D - Immutable](https://leetcode.com/problems/range-sum-query-2d-immutable/) | [304. 二维区域和检索 - 矩阵不可变](https://leetcode.cn/problems/range-sum-query-2d-immutable/) |  🟡   |

```
OUTLINE: 
- 做力扣

力扣303. 区域和检索
	- 暴力解法: for循环, 时间复杂度O(N)
	
	- 前缀和
		> 易错点: 
> - 为什么需要 `preSum = new int[nums.length + 1]` // 方便计算
> - 这次不要nums声明为成员变量
> - preSum需要真正创建数组，会报空指针错误


拓展题: 
- 求每个分数段有多少学生

力扣304. 二维矩阵求前缀和
- 牢记模板

口诀: 上 + 左 - 重复 + 自己

记住: PreSum矩阵也是多构建一行一列

```



> 前缀和技巧适用于快速、频繁地计算一个索引区间内的元素之和。



### 一堆数组中的前缀和

##### 力扣第 303 题「[区域和检索 - 数组不可变](https://leetcode.cn/problems/range-sum-query-immutable/)」

- 计算数组区间内元素的和，这是一道标准的前缀和问题

###### 方法一: 暴力for循环

```java
class NumArray {

    private int[] nums; // 成员变量
  
    public NumArray(int[] nums) {
        this.nums = nums; 
    }
    
    public int sumRange(int left, int right) {
        int sum = 0; 
        for (int i = left ; i <= right; i++){
            sum += nums[i];
        }
        return sum; 
    }
}

```

> 易错点: 
>
> - 需要构造`private int[] nums`

```
为什么已经有 int[] nums 参数了，还要再定义一个 private int[] nums？

关键就在于：

- 构造函数的参数，只在构造函数里面存在。
- 所以 nums 不能再 sumRange 里用
- 所以需要成员变量
	- 定义一个 private int[] nums
	

Private: 只有 NumArray 自己内部的方法可以访问这个变量。

因为名字一样，所以：
this.nums → 成员变量
nums → 参数

> 把参数里的数组，存到对象的成员变量里。
```

这个解法每次调用 `sumRange` 函数时，都要进行一次 for 循环遍历，时间复杂度为 O(N)，而 `sumRange` 的调用频率可能非常高，所以这个算法的效率很低。

###### 方法二: 前缀和

```java
class NumArray {
		
  	private int[] preSum; 
    public NumArray(int[] nums) {
        // 计算 nums 的累加和
      // 计算 nums 的累加和
      preSum = new int[nums.length + 1];
      for(int i = 0 ; i < nums.length; i ++ ){
			preSum[i + 1 ] =  preSum[i] + nums[i];
      }
    }
    
  	
    public int sumRange(int left, int right) {
        int sum = 0; 
      	sum = preSum [right + 1] - preSum [left ]; 
      
      return sum ; 
    }
}

```

> 易错点: 
>
> - 为什么需要 `preSum = new int[nums.length + 1]` // 方便计算
> - 这次不要nums声明为成员变量
> - preSum需要真正创建数组，会报空指针错误



##### 拓展题: 

- 求每个分数段有多少学生

这个技巧在生活中运用也挺广泛的，比方说，你们班上有若干同学，每个同学有一个期末考试的成绩（满分 100 分），那么请你实现一个 API，输入任意一个分数段，返回有多少同学的成绩在这个分数段内。

那么，你可以先通过计数排序的方式计算每个分数具体有多少个同学，然后利用前缀和技巧来实现分数段查询的 API：

它的核心思想是：先统计每个具体分数有多少人（计数排序的思想），然后对这个计数的数组求前缀和。这样，`count[i]` 的含义就从“得分为 i 的人数”变成了“得分在 [0, i] 范围内的总人数”

```java 
// 存储着所有同学的分数
int[] scores = new int[]{...};
// 试卷满分 100 分 - (0 ~ 100分的人数,所以有101种情况)
int[] count = new int[100 + 1];



// 记录每个分数有几个同学
for (int score : scores) {
    count[score]++;
}

//这一步结束后，count[score] 存储的是恰好得到 score 分的同学人数。 例如，如果 count[85] = 3，说明有 3 个人考了 85 分。

// 构造前缀和数组
for (int i = 1; i < count.length; i++) {
    count[i] = count[i] + count[i-1];
}

// 利用 count 这个前缀和数组进行分数段查询

// 查询分数在 [80, 90] 之间的同学有多少人
int result = count[90] - count[79];
```



### 二维数组中的前缀和

##### 304 题「[二维区域和检索 - 矩阵不可变](https://leetcode.cn/problems/range-sum-query-2d-immutable/)」

其实和上一题类似，上一题是计算子数组的元素之和，这道题计算二维矩阵中子矩阵的元素之和：

```markdown
> 先行(row)，后列(col)

我们也会多开一行、多开一列。

0 0 0 0
0 ? ? ?
0 ? ? ?

防止越界

 preSum[i][j] 表示原矩阵左上角到 (i-1, j-1) 的矩形和
```

![1.png](https://pic.leetcode.cn/1614650837-SAIiWg-1.png)

```java
```

时间复杂度：预处理前缀和数组需要对原数组进行线性扫描，复杂度为 O(n∗m)，计算结果复杂度为 O(1)。整体复杂度为 O(n∗m)
空间复杂度：O(n∗m)

#####  二维前缀和模板【重点】

``` java
// 预处理前缀和数组
{
    sum = new int[n + 1][m + 1];
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            // 当前格子(和) = 上方的格子(和) + 左边的格子(和) - 左上角的格子(和) + 当前格子(值)【和是指对应的前缀和，值是指原数组中的值】
            sum[i][j] = sum[i - 1][j] + sum[i][j - 1] - sum[i - 1][j - 1] + matrix[i - 1][j - 1];
        }
    }
}

// 首先我们要令左上角为 (x1, y1) 右下角为 (x2, y2)
// 计算 (x1, y1, x2, y2) 的结果
{
    // 前缀和是从 1 开始，原数组是从 0 开始，上来先将原数组坐标全部 +1，转换为前缀和坐标
    x1++; y1++; x2++; y2++;
    // 记作 22 - 12 - 21 + 11，然后 不减，减第一位，减第二位，减两位
    // 也可以记作 22 - 12(x - 1) - 21(y - 1) + 11(x y 都 - 1)
    ans = sum[x2][y2] - sum[x1 - 1][y2] - sum[x2][y1 - 1] + sum[x1 - 1][y1 - 1];
}

作者：宫水三叶
链接：https://leetcode.cn/problems/range-sum-query-2d-immutable/solutions/629362/xia-ci-ru-he-zai-30-miao-nei-zuo-chu-lai-ptlo/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

##### 答案

```java
class NumMatrix {

    private int[][] preSum; // 保存二维前缀和

    public NumMatrix(int[][] matrix) {

        int m = matrix.length; // 矩阵有 m 行。
        int n = matrix[0].length; //{3,0,1} 一个普通的一维数组。里面有三个数字int, 矩阵有 n 列。

        preSum = new int[m + 1][n + 1]; //多出来的一行一列全部都是 0。

      // 从1开始, 是因为真正的数据从preSum[1][1]
      //上 + 左 - 重复 + 自己
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {

                preSum[i][j]
                        = preSum[i - 1][j]
                        + preSum[i][j - 1]
                        - preSum[i - 1][j - 1]
                        + matrix[i - 1][j - 1];
            }
        }
    }

    public int sumRegion(int row1, int col1, int row2, int col2) {

        return preSum[row2 + 1][col2 + 1]
                - preSum[row1][col2 + 1]
                - preSum[row2 + 1][col1]
                + preSum[row1][col1];
    }
}
```

> 口诀: 上 + 左 - 重复 + 自己
