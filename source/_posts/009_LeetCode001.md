---
title: Leetcode刷题折磨记录
description: 
date: 2022-11-12
top_image: https://assets.leetcode.com/uploads/2021/07/15/lc-domino1.jpg
cover: https://assets.leetcode.com/uploads/2021/07/15/lc-domino1.jpg
categories: 
- Leetcode
tag: 
- Leetcode
- 算法


---



> 有必要说明一下，题目和部分代码皆来自LeetCode，解题思路掺杂了我学习之后的一些想法。很多题目没有被我做出来，所以我看过官方的答案之后记录在此，每个题目都是一个超链接，链接到原题。

# 动态规划问题

## [309. 最佳买卖股票时机含冷冻期](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

给定一个整数数组`prices`，其中第 `prices[i]` 表示第 `i` 天的股票价格 。

设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:

- 卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。

**注意：**你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

**示例 1:**

```
输入: prices = [1,2,3,0,2]
输出: 3 
解释: 对应的交易状态为: [买入, 卖出, 冷冻期, 买入, 卖出]
```

**示例 2:**

```
输入: prices = [1]
输出: 0
```

使用**动态规划**的思想，每天的状态都只有三种，而且每天的状态都与前两天的状态有关。在这个题目里

> 1. 这个状态是指这一天结束时的状态
>
> 2. 股票的收益不是每天计算，当买入时的收益时负收益，而当卖出时候的收益则是正收益。当持有股票是，收益是与买入时收益相等的
>
> - 我们目前持有一支股票，对应的「累计最大收益」记为 `f\[i\][0]f[i\][0]`；
>
>  - 我们目前不持有任何股票，并且处于冷冻期中，对应的「累计最大收益」记为 `f[i\][1]f[i\][1]`；
>
> - 我们目前不持有任何股票，并且不处于冷冻期中，对应的「累计最大收益」记为`f[i\][2]f[i\][2]`。
>
>    

- **手上持有的股票的最大收益**：当天没有买入、卖出或处于冷冻期

  第一种情况：股票是之前买入，收益为`f[i-1\][0]`

  第二种情况：股票是今天买入，那么前一天就不能持有股票并且不能处于冷冻期（就是前一天不能是刚卖），收益为`f[i-1\][2]-prices[i]`

- **目前不持有任何股票，且处于冷冻期（今天刚卖）**：没有交易产生

  收益只有一种：之前持有股票的收益加上卖出的股票的收益 `f[i-1\][0] + prices[i]`
- **目前不持有任何股票，且不处于冷冻期**
  
  第一种情况：因为没有处于冷冻期，所以第 `i-1` 天没有发生操作，并且如果不处于冷冻期，收益为`f[i-1\][2]`
  
  第二种情况：如果`i-1`天处于冷冻期，收益为`f[i-1\][1]`
  
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices:
            return 0
        
        n = len(prices)
        # f[i][0]: 手上持有股票的最大收益
        # f[i][1]: 手上不持有股票，并且处于冷冻期中的累计最大收益
        # f[i][2]: 手上不持有股票，并且不在冷冻期中的累计最大收益
        f = [[-prices[0], 0, 0]] + [[0] * 3 for _ in range(n - 1)]
        for i in range(1, n):
            f[i][0] = max(f[i - 1][0], f[i - 1][2] - prices[i])
            f[i][1] = f[i - 1][0] + prices[i]
            f[i][2] = max(f[i - 1][1], f[i - 1][2])
        
        return max(f[n - 1][1], f[n - 1][2])

```

因为每一天的收益都只与前一天有关，所以 `f` 数组无用的可以省略掉

```py
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices:
            return 0
        
        n = len(prices)
        f0, f1, f2 = -prices[0], 0, 0
        for i in range(1, n):
            newf0 = max(f0, f2 - prices[i])
            newf1 = f0 + prices[i]
            newf2 = max(f1, f2)
            f0, f1, f2 = newf0, newf1, newf2
        
        return max(f1, f2)
```

## [712. 两个字符串的最小ASCII删除和](https://leetcode.cn/problems/minimum-ascii-delete-sum-for-two-strings/)

给定两个字符串`s1` 和 `s2`，返回 *使两个字符串相等所需删除字符的 **ASCII** 值的最小和* 。

**示例 1:**

```
输入: s1 = "sea", s2 = "eat"
输出: 231
解释: 在 "sea" 中删除 "s" 并将 "s" 的值(115)加入总和。
在 "eat" 中删除 "t" 并将 116 加入总和。
结束时，两个字符串相等，115 + 116 = 231 就是符合条件的最小和。
```

**示例 2:**

```
输入: s1 = "delete", s2 = "leet"
输出: 403
解释: 在 "delete" 中删除 "dee" 字符串变成 "let"，
将 100[d]+101[e]+101[e] 加入总和。在 "leet" 中删除 "e" 将 101[e] 加入总和。
结束时，两个字符串都等于 "let"，结果即为 100+101+101+101 = 403 。
如果改为将两个字符串转换为 "lee" 或 "eet"，我们会得到 433 或 417 的结果，比答案更大。
```

这个题实在不会，希望以后看到回想起来重做一次。 直接复制一下官方解答吧

```python
class Solution {
    public int minimumDeleteSum(String s1, String s2) {
        int m = s1.length(), n = s2.length();
        int[][] dp = new int[m + 1][n + 1];
        for (int i = 1; i <= m; i++) {
            dp[i][0] = dp[i - 1][0] + s1.codePointAt(i - 1);
        }
        for (int j = 1; j <= n; j++) {
            dp[0][j] = dp[0][j - 1] + s2.codePointAt(j - 1);
        }
        for (int i = 1; i <= m; i++) {
            int code1 = s1.codePointAt(i - 1);
            for (int j = 1; j <= n; j++) {
                int code2 = s2.codePointAt(j - 1);
                if (code1 == code2) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    dp[i][j] = Math.min(dp[i - 1][j] + code1, dp[i][j - 1] + code2);
                }
            }
        }
        return dp[m][n];
    }
}

作者：力扣官方题解
链接：https://leetcode.cn/problems/minimum-ascii-delete-sum-for-two-strings/solutions/1712998/liang-ge-zi-fu-chuan-de-zui-xiao-asciish-xllf/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## [1218. 最长定差子序列](https://leetcode.cn/problems/longest-arithmetic-subsequence-of-given-difference/)

给你一个整数数组 `arr` 和一个整数 `difference`，请你找出并返回 `arr` 中最长等差子序列的长度，该子序列中相邻元素之间的差等于 `difference` 。

**子序列** 是指在不改变其余元素顺序的情况下，通过删除一些元素或不删除任何元素而从 `arr` 派生出来的序列。

**示例 1：**

```
输入：arr = [1,2,3,4], difference = 1
输出：4
解释：最长的等差子序列是 [1,2,3,4]。
```

**示例 2：**

```
输入：arr = [1,3,5,7], difference = 1
输出：1
解释：最长的等差子序列是任意单个元素。
```

**示例 3：**

```
输入：arr = [1,5,7,8,5,3,4,2,1], difference = -2
输出：4
解释：最长的等差子序列是 [7,5,3,1]。
```

**提示：**

- `1 <= arr.length <= 105`
- `-104 <= arr[i], difference <= 104`

**解题思路：**

将问题转化为 以`arr[i]`为结尾的最长等差子序列的长度，最后从这些中选出最大的值，即为最终结果。

令 `dp[i]` 表示以 `arr[i]` 为结尾的最长的等差子序列的长度，我们可以在 `arr[i]` 左侧找到满足 `arr[j]=arr[i]−d` 的元素，将 `arr[i]` 加到以 `arr[j]` 为结尾的最长的等差子序列的末尾，这样可以递推地从 `dp[j]` 计算出 `dp[i]`。由于我们是从左往右遍历 `arr` 的，对于两个相同的元素，下标较大的元素对应的 `dp` 值不会小于下标较小的元素对应的 `dp` 值，因此下标`j` 可以取满足 `j<i` 且 `arr[j]=arr[i]−d` 的所有下标的最大值。故有转移方程
$$
dp[i]=dp[j]+1
$$
由于我们总是在左侧找一个最近的等于`arr[i]−d` 元素并取其对应 `dp` 值，因此我们直接用`dp[v]` 表示以 `v` 为结尾的最长的等差子序列的长度，这样 `dp[v−d]` 就是我们要找的左侧元素对应的最长的等差子序列的长度，因此转移方程可以改为
$$
dp[v]=dp[v−d]+1
$$
最后答案为`max{dp}`。

```python
class Solution:
    def longestSubsequence(self, arr: List[int], difference: int) -> int:
        dp = defaultdict(int)
        for v in arr:
            dp[v] = dp[v - difference] + 1
        return max(dp.values())
```

## [790.多米诺和托米诺平铺](https://leetcode.cn/problems/domino-and-tromino-tiling/description/)

有两种形状的瓷砖：一种是 `2 x 1` 的多米诺形，另一种是形如 "L" 的托米诺形。两种形状都可以旋转。

![img](https://assets.leetcode.com/uploads/2021/07/15/lc-domino.jpg)

给定整数 n ，返回可以平铺 `2 x n` 的面板的方法的数量。**返回对** `109 + 7` **取模** 的值。

平铺指的是每个正方形都必须有瓷砖覆盖。两个平铺不同，当且仅当面板上有四个方向上的相邻单元中的两个，使得恰好有一个平铺有一个瓷砖占据两个正方形。

**示例 1:**

![img](https://assets.leetcode.com/uploads/2021/07/15/lc-domino1.jpg)

```
输入: n = 3
输出: 5
解释: 五种不同的方法如上所示。
```

**示例 2:**

```
输入: n = 1
输出: 1
```



**提示：**

- `1 <= n <= 1000`

官方解析实在是通俗不易懂，好在找到了一个通俗易懂的解析版本--[智商局局长](https://leetcode.cn/problems/domino-and-tromino-tiling/solutions/1963121/xiao-bai-yi-dong-de-guan-fang-ti-jie-jie-7tjg/)

**解析**

我们将平铺看成一个从左到右铺上瓷砖的过程，第 i 列之前都被瓷砖覆盖，在第 i 列之后都没有被瓷砖覆盖。那么第 i 列的正方形有四种被覆盖的情况：

一个正方形都没有被覆盖，记为状态 0；
只有上方的正方形被覆盖，记为状态 1；
只有下方的正方形被覆盖，记为状态 2；
上下两个正方形都被覆盖，记为状态 3。

然后，忘了官方题解的图中那个以四块正方形组成的大正方形，只聚焦于两个正方形组成的第i列。

![image-20221112102953771](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/11/12_10_29_57_image-20221112102953771.png)

我们使用dp\[i][n]来表示将瓷砖铺成第i列为n状态的所有方法。

如果第i列的状态为0，即一个瓷砖都没有，因为第i列之前都被瓷砖覆盖了，所以其状态是与第i-1列的3状态相同的，即dp\[i][0]=dp\[i-1][3]。

如果第i列的状态为1，即只有上方有一个瓷砖，要想成为这种情况，有两种方式：在第i-1为2状态下时，横着铺一块多米诺形瓷砖，或者在i第i-1列为0状态下时铺一个托米诺形瓷砖，即dp\[i][1]=dp\[i-1][0]+dp\[i-1][2]。

如果第i列的状态为2，即只有下方的一个瓷砖，与状态为1同理。有两种方式：在第i-1为1状态下时，横着铺一块多米诺形瓷砖，或者在i第i-1列为0状态下时铺一个托米诺形瓷砖，即dp[i][1]=dp\[i-1][0]+dp\[i-1][1]。

如果第i列的状态为3，即第i列已经铺满了瓷砖，此时需要在第i-1列的3状态下竖着铺一块多米诺形瓷砖，或者在第i-1列的1、2状态下铺一个托米诺形瓷砖，dp\[i][3]=dp\[i-1][0]+dp\[i-1][1]+dp\[i-1][2]+dp\[i-1][3]

不难注意到，当i=0时，i-1会越界，所以需要为i=0时专门赋值。dp[i][0]是一块砖都没有，自然为0；因为多米诺形瓷砖与托米诺形瓷砖都无法在第0列铺出一个瓷砖，所以dp[i][1]与dp[i][2]也是0；使用多米诺形瓷砖时可以铺满第0列，所以dp[i][3]的值为1。

最后是官方题解的代码：

```python
class Solution:
    def numTilings(self, n: int) -> int:
        MOD = 10 ** 9 + 7
        dp = [[0] * 4 for _ in range(n + 1)]
        dp[0][3] = 1
        for i in range(1, n + 1):
            dp[i][0] = dp[i - 1][3]
            dp[i][1] = (dp[i - 1][0] + dp[i - 1][2]) % MOD
            dp[i][2] = (dp[i - 1][0] + dp[i - 1][1]) % MOD
            dp[i][3] = (((dp[i - 1][0] + dp[i - 1][1]) % MOD + dp[i - 1][2]) % MOD + dp[i - 1][3]) % MOD
        return dp[n][3]

作者：力扣官方题解
链接：https://leetcode.cn/problems/domino-and-tromino-tiling/solutions/1962465/duo-mi-nuo-he-tuo-mi-nuo-ping-pu-by-leet-7n0j/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```



# 132模式

## [456. 132 模式](https://leetcode.cn/problems/132-pattern/)

给你一个整数数组 `nums` ，数组中共有 `n` 个整数。**132 模式的子序列** 由三个整数 `nums[i]`、`nums[j]` 和 `nums[k]` 组成，并同时满足：`i < j < k` 和 `nums[i] < nums[k] < nums[j]` 。

如果 `nums` 中存在 **132 模式的子序列** ，返回 `true` ；否则，返回 `false` 。

**示例 1：**

```
输入：nums = [1,2,3,4]
输出：false
解释：序列中不存在 132 模式的子序列。
```

**示例 2：**

```
输入：nums = [3,1,4,2]
输出：true
解释：序列中有 1 个 132 模式的子序列： [1, 4, 2] 。
```

**示例 3：**

```
输入：nums = [-1,3,2,0]
输出：true
解释：序列中有 3 个 132 模式的的子序列：[-1, 3, 2]、[-1, 3, 0] 和 [-1, 2, 0] 。
```

**一：枚举 3**

  3 的位置处于 1 和 2 之间，将数组分为 3 块，第一块为 1 维护的数组 `nums[0...j-1]`，第二块为 `nums[j]`,第三块为`nums[j+1...n-1]`。

因为 1 是 132 模式中的最小值，所以第一块只要每次维护好最小值 `a[i]`。第三块需要对元素进行排序，维护其为一个有序集合。当确定 `a[i]`与`a[j]`时，找到在其中比`a[i]`大的最小值，然后对比`a[j]`与`a[k]`的大小，如果符合 132 模式则返回为 True ，否则从第三块中移除`num[j+1]`(下一轮被枚举的j)，并维护第一块的最小值。

```py
class Solution:
    def find132pattern(self, nums: List[int]) -> bool:
        n = len(nums)
        if n < 3:
            return False
        
        # 左侧最小值
        left_min = nums[0]
        # 右侧所有元素
        right_all = SortedList(nums[2:])
        
        for j in range(1, n - 1):
            if left_min < nums[j]:
                index = right_all.bisect_right(left_min)
                if index < len(right_all) and right_all[index] < nums[j]:
                    return True
            left_min = min(left_min, nums[j])
            right_all.remove(nums[j + 1])

        return False

```

二：枚举1

主要思路：从数组右面往左枚举，在 132 模式中，如果 `3 > 2`, `2 > 1`那么 `3 > 1`,在枚举的时候，使用单调栈的方式。

>单调栈：从栈底到栈顶的元素都是严格单调递减或递增的。当给定阈值 x*x* 时，我们只需要不断地弹出栈顶的元素，直到栈为空或者 x*x* 严格小于栈顶元素。此时我们再将 x*x* 入栈，这样就维护了栈的单调性。

- 我们用单调栈维护所有可以作为 2的候选元素。初始时，单调栈中只有唯一的元素`a[n−1]`。我们还需要使用一个变量 `max_k` 记录所有可以真正作为 2 的元素的最大值；

- 随后我们从 `n−2` 开始从右到左枚举元素 `a[i]`：

- 首先我们判断 `a[i]` 是否可以作为 1。如果 `a[i]<max_k`，那么它就可以作为 1，我们就找到了一组满足 132 模式的三元组；

- 随后我们判断 `a[i]` 是否可以作为 3，以此找出哪些可以真正作为 2 的元素。我们将 `a[i]` 不断地与单调栈栈顶的元素进行比较，如果 `a[i]` 较大，那么栈顶元素可以真正作为 2，将其弹出并更新 `max_k`；

- 最后我们将 `a[i]` 作为 2 的候选元素放入单调栈中。这里可以进行一个优化，即如果 `a[i]≤max_k`，那么我们也没有必要将 `a[i]` 放入栈中，因为即使它在未来被弹出，也不会将 `max_k` 更新为更大的值。

- 在枚举完所有的元素后，如果仍未找到满足 `132` 模式的三元组，那就说明其不存在。

```python 
class Solution {
public:
    bool find132pattern(vector<int>& nums) {
        int n = nums.size();
        stack<int> candidate_k;
        candidate_k.push(nums[n - 1]);
        int max_k = INT_MIN;

        for (int i = n - 2; i >= 0; --i) {
            if (nums[i] < max_k) {
                return true;
            }
            while (!candidate_k.empty() && nums[i] > candidate_k.top()) {
                max_k = candidate_k.top();
                candidate_k.pop();
            }
            if (nums[i] > max_k) {
                candidate_k.push(nums[i]);
            }
        }

        return false;
    }
};
```

# 单调栈

## [739. 每日温度](https://leetcode.cn/problems/daily-temperatures/)

给定一个整数数组 `temperatures` ，表示每天的温度，返回一个数组 `answer` ，其中 `answer[i]` 是指对于第 `i` 天，下一个更高温度出现在几天后。如果气温在这之后都不会升高，请在该位置用 `0` 来代替。

**示例 1:**

```
输入: temperatures = [73,74,75,71,69,72,76,73]
输出: [1,1,4,2,1,1,0,0]
```

**示例 2:**

```
输入: temperatures = [30,40,50,60]
输出: [1,1,1,0]
```

**示例 3:**

```
输入: temperatures = [30,60,90]
输出: [1,1,0]
```

**提示：**

- `1 <= temperatures.length <= 105`
- `30 <= temperatures[i] <= 100`

**一：暴力解题**

从提示中我们可以看到，温度是在 30-100 之间的，所以我们只要维护一个温度列表，反向遍历温度列表，每当温度出现便更新一次，这样温度列表里一直是最近的温度下标。

反向遍历温度列表。对于每个元素 `temperatures[i]`，在数组 `next` 中找到从 `temperatures[i] + 1` 到 100 中每个温度第一次出现的下标，将其中的最小下标记为 `warmerIndex`，则 `warmerIndex` 为下一次温度比当天高的下标。如果 `warmerIndex` 不为无穷大，则 `warmerIndex - i` 即为下一次温度比当天高的等待天数，最后令 `next[temperatures[i]] = i`。

```python
class Solution:
    def dailyTemperatures(self, temperatures: List[int]) -> List[int]:
        n = len(temperatures)
        ans, nxt, big = [0] * n, dict(), 10**9
        for i in range(n - 1, -1, -1):
            warmer_index = min(nxt.get(t, big) for t in range(temperatures[i] + 1, 102))
            if warmer_index != big:
                ans[i] = warmer_index - i
            nxt[temperatures[i]] = i
        return ans
```

**二：单调栈**

维护一个存储温度和下标的单调栈，栈底到栈顶依次单调递减。如果一个下标仍存在单调栈里，说明目前尚未找到温度更高的下标。

正向遍历温度列表，如果单调栈里为空，则直接入栈。如果单调栈里元素不为空，循环比较当前元素`temperatures[i]`与栈顶的温度大小，如果大于栈顶元素的温度，则栈顶元素出栈，并修改将栈顶元素对应的`ans`等待天数为`i-stack[-1][0]`,如果小于栈顶元素的温度，则当前元素入栈。

由于单调栈满足从栈底到栈顶元素对应的温度递减，因此每次有元素进栈时，会将温度更低的元素全部移除，并更新出栈元素对应的等待天数，这样可以确保等待天数一定是最小的

```python
class Solution:
    def dailyTemperatures(self, temperatures: List[int]) -> List[int]:
        ans = [0] * len(temperatures)
        stack = []
        for i in range(len(temperatures)):
            if stack:
                while stack and temperatures[i] > stack[-1][1]:
                    ans[stack[-1][0]] = i - stack[-1][0]
                    stack.pop()
                stack.append([i, temperatures[i]])
            else:
                stack.append([i, temperatures[i]])

        return ans
```

