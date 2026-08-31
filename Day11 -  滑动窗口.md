# 滑动窗口

|                           LeetCode                           |                             力扣                             | 难度 |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :--: |
| [76. Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/) | [76. 最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/) |      |
| [567. Permutation in String](https://leetcode.com/problems/permutation-in-string/) | [567. 字符串的排列](https://leetcode.cn/problems/permutation-in-string/) |      |
| [438. Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/) | [438. 找到字符串中所有字母异位词](https://leetcode.cn/problems/find-all-anagrams-in-a-string/) |      |
| [3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/) | [3. 无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/) |      |

> 动画讲解

双指针 ----> 滑动窗口

滑动窗口可以归为快慢双指针，一快一慢两个指针前后相随，中间的部分就是窗口。**滑动窗口算法技巧主要用来解决子数组问题，比如让你寻找符合某个条件的最长/最短子数组**。



## 滑动窗口框架

滑动窗口算法技巧的思路也不难，就是维护一个窗口，不断滑动，然后更新答案，该算法的大致逻辑如下:

```java
// 索引区间 [left, right) 是窗口
int left = 0, right = 0;

while (right < nums.size()) {
    // 增大窗口
    window.addLast(nums[right]);
    right++;
    
    while (window needs shrink) {
        // 缩小窗口
        window.removeFirst(nums[left]);
        left++;
    }
}
```

基于滑动窗口算法框架写出的代码，时间复杂度是 O(N)，比嵌套 for 循环的暴力解法效率高

```markdown
> 为什么是 O(N)

简单说，指针 left, right 不会回退（它们的值只增不减），所以字符串/数组中的每个元素都只会进入窗口一次，然后被移出窗口一次，不会说有某些元素多次进入和离开窗口，所以算法的时间复杂度就和字符串/数组的长度成正比。

反观嵌套 for 循环的暴力解法，那个 j 会回退，所以某些元素会进入和离开窗口多次，所以时间复杂度就是 O(N^2)
```



```markdown
> 为啥滑动窗口能在 O(N) 的时间穷举子数组？

这个问题本身就是错误的，滑动窗口并不能穷举出所有子串。要想穷举出所有子串，必须用那个嵌套 for 循环。

然而对于某些题目，并不需要穷举所有子串，就能找到题目想要的答案。滑动窗口就是这种场景下的一套算法模板，帮你对穷举过程进行剪枝优化，避免冗余计算。

所以在 算法的本质 中我把滑动窗口算法归为「如何聪明地穷举」一类。
```



### 滑动窗口模板代码

```java
// 滑动窗口算法伪码框架
void slidingWindow(String s) {
    // 用合适的数据结构记录窗口中的数据，根据具体场景变通
    // 比如说，我想记录窗口中元素出现的次数，就用 map
    // 如果我想记录窗口中的元素和，就可以只用一个 int
    Object window = ...
    
    int left = 0, right = 0;
    while (right < s.length()) {
        // c 是将移入窗口的字符
        char c = s[right];
        window.add(c)
        // 增大窗口
        right++;
        // 进行窗口内数据的一系列更新
        ...

        // *** debug 输出的位置 ***
        // 注意在最终的解法代码中不要 print
        // 因为 IO 操作很耗时，可能导致超时
        printf("window: [%d, %d)\n", left, right);
        // ***********************

        // 判断左侧窗口是否要收缩
        while (left < right && window needs shrink) {
            // d 是将移出窗口的字符
            char d = s[left];
            window.remove(d)
            // 缩小窗口
            left++;
            // 进行窗口内数据的一系列更新
            ...
        }
    }
}
```

**框架中两处 `...` 表示的更新窗口数据的地方，在具体的题目中，你需要做的就是往这里面填代码逻辑**。而且，这两个 `...` 处的操作分别是扩大和缩小窗口的更新操作，等会你会发现它们操作是完全对称的。

基于这个框架，遇到子串/子数组相关的题目，你只需要回答以下三个问题：

1、什么时候应该移动 right 扩大窗口？窗口加入字符时，应该更新哪些数据？

2、什么时候窗口应该暂停扩大，开始移动 left 缩小窗口？从窗口移出字符时，应该更新哪些数据？

3、什么时候应该更新结果？

只要能回答这三个问题，就说明可以使用滑动窗口技巧解题。



### 力扣

#### 力扣76.最小覆盖字符串

```
维护一个左闭右开的区间

理论上你可以设计两端都开或者两端都闭的区间，但设计为左闭右开区间是最方便处理的。

因为这样初始化 left = right = 0 时区间 [0, 0) 中没有元素，但只要让 right 向右移动（扩大）一位，区间 [0, 1) 就包含一个元素 0 了。

如果你设置为两端都开的区间，那么让 right 向右移动一位后开区间 (0, 1) 仍然没有元素；如果你设置为两端都闭的区间，那么初始区间 [0, 0] 就包含了一个元素。这两种情况都会给边界处理带来不必要的麻烦。
```

#### 滑动窗口算法思路: 

1. 我们在字符串 `S` 中使用双指针中的左右指针技巧，初始化 `left = right = 0`，把索引**左闭右开**区间 `[left, right)` 称为一个「窗口」。
2. 我们先不断地增加 `right` 指针扩大窗口 `[left, right)`，直到窗口中的字符串符合要求（包含了 `T` 中的所有字符）。
3. 此时，我们停止增加 `right`，转而不断增加 `left` 指针缩小窗口 `[left, right)`，直到窗口中的字符串不再符合要求（不包含 `T` 中的所有字符了）。同时，每次增加 `left`，我们都要更新一轮结果。
4. 重复第 2 和第 3 步，直到 `right` 到达字符串 `S` 的尽头。



#### 框架

首先，初始化 `window` 和 `need` 两个哈希表，记录窗口中的字符和需要凑齐的字符：

```java
// 记录 window 中的字符出现次数
HashMap<Character, Integer> window = new HashMap<>();
// 记录所需的字符出现次数
HashMap<Character, Integer> need = new HashMap<>();

// getOrDefault(c, 0) 的意思是：如果 map 里已经有 c，就获取它的值；如果没有，就返回默认值 0
// 然后 +1，再 put 回去。这就实现了计数的累加
for (int i = 0; i < t.length(); i++) {
    char c = t.charAt(i);
    need.put(c, need.getOrDefault(c, 0) + 1);
}
```

**`need` (需求表)**：记录目标字符串 `t` 中每个字符出现的次数。它是**固定不变**的基准。

- 比如 `t = "ABC"`，那么 `need` 就是 `{'A':1, 'B':1, 'C':1}`。
- 比如 `t = "AAB"`，那么 `need` 就是 `{'A':2, 'B':1}`

**`window` (窗口表)**：记录当前滑动窗口 `[left, right)` 中，各个字符出现的次数。它是**动态变化**的。

- 随着 `right` 指针右移，新字符进入窗口，`window` 中对应字符的计数加 1。
- 随着 `left` 指针右移，旧字符离开窗口，`window` 中对应字符的计数减 1。



>  为什么要这么设计？

在后续的滑动过程中，我们需要快速判断：**“当前窗口里的字符，是否已经覆盖了 `t` 中的所有字符？”**

通过对比 `window` 和 `need` 就可以实现：

- 对于 `t` 中的某个字符 `c`，如果 `window.get(c) >= need.get(c)`，说明窗口里这个字符的数量够了。
- 文章中使用了一个变量 `valid` 来记录有多少种字符已经满足了数量要求。当 `valid == need.size()` 时，就说明窗口已经合法了（包含了 `t` 的所有字符）。

**现在开始套模板，只需要思考以下几个问题**：

1、什么时候应该移动 `right` 扩大窗口？窗口加入字符时，应该更新哪些数据？

2、什么时候窗口应该暂停扩大，开始移动 `left` 缩小窗口？从窗口移出字符时，应该更新哪些数据？

3、我们要的结果应该在扩大窗口时还是缩小窗口时进行更新？

如果一个字符进入窗口，应该增加 `window` 计数器；如果一个字符将移出窗口的时候，应该减少 `window` 计数器；当 `valid` 满足 `need` 时应该收缩窗口；应该在收缩窗口的时候更新最终结果。



### 完整代码

```java
class Solution {
    public String minWindow(String s, String t) {
        Map<Character, Integer> need = new HashMap<>();
        Map<Character, Integer> window = new HashMap<>();
        for (char c : t.toCharArray()) {
            need.put(c, need.getOrDefault(c, 0) + 1);
        }

        int left = 0, right = 0;
        int valid = 0;
        // 记录最小覆盖子串的起始索引及长度
        int start = 0, len = Integer.MAX_VALUE;
        while (right < s.length()) {
            // c 是将移入窗口的字符
            char c = s.charAt(right);
            // 扩大窗口
            right++;
            // 进行窗口内数据的一系列更新
            if (need.containsKey(c)) {
                window.put(c, window.getOrDefault(c, 0) + 1);
                if (window.get(c).equals(need.get(c)))
                    valid++;
            }

            // 判断左侧窗口是否要收缩
            while (valid == need.size()) {
                // 在这里更新最小覆盖子串
                if (right - left < len) {
                    start = left;
                    len = right - left;
                }
                // d 是将移出窗口的字符
                char d = s.charAt(left);
                // 缩小窗口
                left++;
                // 进行窗口内数据的一系列更新
                if (need.containsKey(d)) {
                    if (window.get(d).equals(need.get(d)))
                        valid--;
                    window.put(d, window.get(d) - 1);
                }                    
            }
        }
        // 返回最小覆盖子串
        return len == Integer.MAX_VALUE ? "" : s.substring(start, start + len);
    }
}
```



> 使用 Java 的读者请注意
>
> 对 Java 包装类进行比较时要尤为小心，`Integer`，`String` 等类型应该用 `equals` 方法判定相等，而不能直接用等号 `==`，否则会出错。所以在缩小窗口更新数据的时候，不能直接写为 `window.get(d) == need.get(d)`，而要用 `window.get(d).equals(need.get(d))`，之后的题目代码同理。

上面的代码中，当我们发现某个字符在 `window` 的数量满足了 `need` 的需要，就要更新 `valid`，表示有一个字符已经满足要求。而且，你能发现，两次对窗口内数据的更新操作是完全对称的。

当 `valid == need.size()` 时，说明 `T` 中所有字符已经被覆盖，已经得到一个可行的覆盖子串，现在应该开始收缩窗口了，以便得到「最小覆盖子串」。

移动 `left` 收缩窗口时，窗口内的字符都是可行解，所以应该在收缩窗口的阶段进行最小覆盖子串的更新，以便从可行解中找到长度最短的最终结果。

至此，应该可以完全理解这套框架了，滑动窗口算法又不难，就是细节问题让人烦得很。**以后遇到滑动窗口算法，你就按照这框架写代码，保准没有 bug，还省事儿**