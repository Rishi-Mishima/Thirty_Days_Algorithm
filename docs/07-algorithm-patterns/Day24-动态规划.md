读完本文，你不仅学会了算法套路，还可以顺便解决如下题目：

|                           LeetCode                           |                             力扣                             | 难度 |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :--: |
| [509. Fibonacci Number](https://leetcode.com/problems/fibonacci-number/) | [509. 斐波那契数](https://leetcode.cn/problems/fibonacci-number/) |      |
| [70. Climbing Stairs](https://leetcode.com/problems/climbing-stairs/) | [70. 爬楼梯](https://leetcode.cn/problems/climbing-stairs/)  |      |
| [322. Coin Change](https://leetcode.com/problems/coin-change/) |  [322. 零钱兑换](https://leetcode.cn/problems/coin-change/)  |      |

### 动态规划问题（Dynamic Programming）

首先，**动态规划问题的一般形式就是求最值**。动态规划其实是运筹学的一种最优化方法，只不过在计算机问题上应用比较多，比如说让你求最长递增子序列呀，最小编辑距离呀等等。

> **求解动态规划的核心问题是穷举**。

- 只有列出**正确的「状态转移方程」**，才能正确地穷举
- 而且，你需要判断算法问题是否**具备「最优子结构」**，是否能够通过子问题的最值得到原问题的最值
- 另外，动态规划问题**存在「重叠子问题」**，如果暴力穷举的话效率会很低，所以需要你使用「备忘录」或者「DP table」来优化穷举过程，避免不必要的计算。

#### 模板

```python 
# 自顶向下递归的动态规划
def dp(状态1, 状态2, ...):
    for 选择 in 所有可能的选择:
        # 此时的状态已经因为做了选择而改变
        result = 求最值(result, dp(状态1, 状态2, ...))
    return result

# 自底向上迭代的动态规划
# 初始化 base case
dp[0][0][...] = base case
# 进行状态转移
for 状态1 in 状态1的所有取值：
    for 状态2 in 状态2的所有取值：
        for ...
            dp[状态1][状态2][...] = 求最值(选择1，选择2...)
```



### 斐波那契数列

#### 暴力递归

```java
// f(n) 计算第 n 个斐波那契数
int fib(int n) {
    // base case
    if (n == 0 || n == 1){
        return n;
    }
    return fib(n - 1) + fib(n - 2);
}
```

这个算法的时间复杂度为二者相乘，即 O(2^n)，指数级别，爆炸

- 重叠子问题

#### 带备忘录的递归解法

对于斐波那契数列问题，我们需要一个备忘录记录子问题 `f(x)` 的值，其中 `x` 是一个非负整数，所以一般用一个一维数组 `memo` 充当备忘录就可以了，让 `memo[x]` 存储子问题 `f(x)` 的返回值。

- 你也可以用一个哈希表来存储

```java
int fib(int n) {
    // 备忘录全初始化为 -1
    // 因为斐波那契数肯定是非负整数，所以初始化为特殊值 -1 表示未计算

    // 因为数组的索引从 0 开始，所以需要 n + 1 个空间
    // 这样才能把 `f(0) ~ f(n)` 都记录到 memo 中
    int[] memo = new int[n + 1];
    Arrays.fill(memo, -1);

    return dp(memo, n);
}

// 带着备忘录进行递归
int dp(int[] memo, int n) {
    // base case
    if (n == 0 || n == 1) {
        return n;
    }
    // 已经计算过，不用再计算了
    if (memo[n] != -1) {
        return memo[n];
    }
    // 在返回结果之前，存入备忘录
    memo[n] = dp(memo, n - 1) + dp(memo, n - 2);
    return memo[n];
}
```



#### 自顶向下 vs 自底向上

实际上，动态规划解法确实有两种表现形式：

第一种是带备忘录的递归解法，或称为「自顶向下」的解法，也就是我们上面展示的，一个递归函数带一个 `memo` 备忘录。

第二种是 DP table 的迭代解法，或称为「自底向上」的解法，也就是你说的，用 for 循环去迭代 `dp` 数组进行求解。

**这两者的本质是一样的，可以互相转化。迭代解法中的那个 `dp` 数组，就是递归解法中的 `memo` 数组**。



#### `dp` 数组的迭代（递推）解法

有了上一步的启发，我们不再使用递归函数，直接创建一个数组（DP table），用一个 for 循环从 base case 开始从左到右进行计算即可。

```java
int fib(int n) {
    if (n == 0 || n == 1) {
        return n;
    }
    // dp table
    int[] dp = new int[n + 1];
    // base case
    dp[0] = 0; dp[1] = 1;
    // 状态转移
    for (int i = 2; i <= n; i++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }

    return dp[n];
}
```

其实算法的核心思想都是简单朴素的，只是算法题要通过层层包装，把这些核心思想给你隐藏起来。比如你可以做一下 [70. 爬楼梯](https://leetcode.cn/problems/climbing-stairs/)，它看起来是一个场景题，但本质就是考察斐波那契数列。

斐波那契数列的例子严格来说不算动态规划，因为没有涉及求最值，以上旨在说明重叠子问题的消除方法，演示得到最优解法逐步求精的过程。下面，看第二个例子，凑零钱问题。



### 凑零钱

这是力扣第 322 题「[零钱兑换](https://leetcode.cn/problems/coin-change/)」：

给你 `k` 种面值的硬币，面值分别为 `c1, c2 ... ck`，每种硬币的数量无限，再给一个总金额 `amount`，问你**最少**需要几枚硬币凑出这个金额，如果不可能凑出，算法返回 -1 。算法的函数签名如下：

#### 暴力递归

**所以我们可以这样定义 `dp` 函数：`dp(n)` 表示，输入一个目标金额 `n`，返回凑出目标金额 `n` 所需的最少硬币数量**。

```java
// 伪码框架
int coinChange(int[] coins, int amount) {
    // 题目要求的最终结果是 dp(amount)
    return dp(coins, amount);
}

// 定义：要凑出金额 n，至少要 dp(coins, n) 个硬币
int dp(int[] coins, int n) {
    // 做选择，选择需要硬币最少的那个结果
    for (int coin : coins) {
        res = min(res, 1 + subProblem);
    }
    return res;
}
```

根据伪码，我们加上 base case 即可得到最终的答案。显然目标金额为 0 时，所需硬币数量为 0；当目标金额小于 0 时，无解，返回 -1：

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        // 题目要求的最终结果是 dp(amount)
        return dp(coins, amount);
    }

    // 定义：要凑出目标金额 amount，至少要 dp(coins, amount) 个硬币
    int dp(int[] coins, int amount) {
        // base case
        if (amount == 0) return 0;
        if (amount < 0) return -1;

        int res = Integer.MAX_VALUE;
        for (int coin : coins) {
            // 计算子问题的结果
            int subProblem = dp(coins, amount - coin);
            // 子问题无解则跳过
            if (subProblem == -1) continue;
            // 在子问题中选择最优解，然后加一
            res = Math.min(res, subProblem + 1);
        }

        return res == Integer.MAX_VALUE ? -1 : res;
    }
}
```



> **递归算法的时间复杂度分析：子问题总数 x 解决每个子问题所需的时间**。



#### 带备忘录的递归

类似之前斐波那契数列的例子，只需要稍加修改，就可以通过备忘录消除子问题：

```java
class Solution {
    int[] memo;

    public int coinChange(int[] coins, int amount) {
        memo = new int[amount + 1];
        // 备忘录初始化为一个不会被取到的特殊值，代表还未被计算
        Arrays.fill(memo, -666);

        return dp(coins, amount);
    }

    int dp(int[] coins, int amount) {
        if (amount == 0) return 0;
        if (amount < 0) return -1;
        // 查备忘录，防止重复计算
        if (memo[amount] != -666)
            return memo[amount];

        int res = Integer.MAX_VALUE;
        for (int coin : coins) {
            // 计算子问题的结果
            int subProblem = dp(coins, amount - coin);
            // 子问题无解则跳过
            if (subProblem == -1) continue;
            // 在子问题中选择最优解，然后加一
            res = Math.min(res, subProblem + 1);
        }
        // 把计算结果存入备忘录
        memo[amount] = (res == Integer.MAX_VALUE) ? -1 : res;
        return memo[amount];
    }
}
```



#### `dp` 数组的迭代解法

`dp` 数组的定义和刚才 `dp` 函数类似，也是把「状态」，也就是目标金额作为变量。不过 `dp` 函数体现在函数参数，而 `dp` 数组体现在数组索引：

**`dp` 数组的定义：当目标金额为 `i` 时，至少需要 `dp[i]` 枚硬币凑出**。

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int[] dp = new int[amount + 1];
        // 数组大小为 amount + 1，初始值也为 amount + 1
        Arrays.fill(dp, amount + 1);

        // base case
        dp[0] = 0;
        // 外层 for 循环在遍历所有状态的所有取值
        for (int i = 0; i < dp.length; i++) {
            // 内层 for 循环在求所有选择的最小值
            for (int coin : coins) {
                // 子问题无解，跳过
                if (i - coin < 0) {
                    continue;
                }
                dp[i] = Math.min(dp[i], 1 + dp[i - coin]);
            }
        }
        return (dp[amount] == amount + 1) ? -1 : dp[amount];
    }
}
```

<img src="https://labuladong.online/images/algo/dynamic-programming/6.jpg" alt="img" style="zoom:50%;" />