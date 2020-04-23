

递归和动态规划都是将原问题拆成多个子问题然后求解，他们之间最本质的区别是，动态规划保存了子问题的解，避免重复计算。

# 斐波那契数列

## 1. 爬楼梯

70\. Climbing Stairs (Easy)

[Leetcode](https://leetcode.com/problems/climbing-stairs/description/) / [力扣](https://leetcode-cn.com/problems/climbing-stairs/description/)

题目描述：有 N 阶楼梯，每次可以上一阶或者两阶，求有多少种上楼梯的方法。

题解：定义一个数组 dp 存储上楼梯的方法数（为了方便讨论，数组下标从 1 开始），dp[i] 表示走到第 i 个楼梯的方法数目。

第 i 个楼梯可以从第 i-1 和 i-2 个楼梯再走一步到达，走到第 i 个楼梯的方法数为走到第 i-1 和第 i-2 个楼梯的方法数之和。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/14fe1e71-8518-458f-a220-116003061a83.png" width="200px"> </div><br>

考虑到 dp[i] 只与 dp[i - 1] 和 dp[i - 2] 有关，因此可以只用两个变量来存储 dp[i - 1] 和 dp[i - 2]，使得原来的 O(N) 空间复杂度优化为 O(1) 复杂度。

```java
class Solution {
public:
    int climbStairs(int n) {
        if(n <= 2)
            return n;

        int pre2 = 1, pre1 = 2, sum; // 从1开始
        for(int i = 3; i <= n; ++i) {
            sum = pre1 + pre2;
            pre2 = pre1;
            pre1 = sum;
        }
        return sum;
    }
};
```

## 2. 强盗抢劫

198\. House Robber (Easy)

[Leetcode](https://leetcode.com/problems/house-robber/description/) / [力扣](https://leetcode-cn.com/problems/house-robber/description/)

题目描述：抢劫一排住户，但是不能抢邻近的住户，求最大抢劫量。

**[题解](https://labuladong.github.io/ebook/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92%E7%B3%BB%E5%88%97/%E6%8A%A2%E6%88%BF%E5%AD%90.html)**：定义 dp 数组用来存储最大的抢劫量，其中 dp[i] 表示抢到第 i 个住户时的最大抢劫量。

由于不能抢劫邻近住户，如果抢劫了第 i -1 个住户，那么就不能再抢劫第 i 个住户，所以所以在当前位置 i 房屋可盗窃的最大值，要么就是 i-1 房屋可盗窃的最大值，要么就是 i-2 房屋可盗窃的最大值加上当前房屋的值，二者之间取最大值:

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/2de794ca-aa7b-48f3-a556-a0e2708cb976.jpg" width="350px"> </div><br>
选择 dp[–1] = dp[0] = 0 为初始情况，可简化代码。

```java
class Solution {
public:
    int rob(vector<int>& nums) {
        if(nums.empty()) return 0;
        
        int pre2 = 0, pre1 = 0, curr; // 已知的是偷窃-1个房屋和0个房屋，金额均为0
        for(int i = 0; i < nums.size(); ++i) {
            curr = max((pre2 + nums[i]), pre1);
            pre2 = pre1;
            pre1 = curr;
        }
        return curr;
    }
};
```

## 3. 强盗在环形街区抢劫

213\. House Robber II (Medium)

[Leetcode](https://leetcode.com/problems/house-robber-ii/description/) / [力扣](https://leetcode-cn.com/problems/house-robber-ii/description/)

[**题解**](https://labuladong.github.io/ebook/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92%E7%B3%BB%E5%88%97/%E6%8A%A2%E6%88%BF%E5%AD%90.html)：环状排列意味着第一个房子和最后一个房子中只能选择一个偷窃，因此可以把此环状排列房间问题简化为两个单排排列房间子问题：

1、在不偷窃第一个房子的情况下（即 nums[1:]），最大金额是 p1 
2、在不偷窃最后一个房子的情况下（即 nums[:n-1]），最大金额是 p2
综合偷窃最大金额： 为以上两种情况的较大值，即 max(p1,p2)。

```java
class Solution {
public:
    int rob(vector<int>& nums) {
        int len = nums.size();
        if(len == 0) return 0;
        if(len == 1) return nums[0];

        return max(rob(nums, 0, len-2), rob(nums, 1, len-1));
    }

    int rob(vector<int>& nums, int start, int end) {
        int pre2 = 0, pre1 = 0, curr;
        for(int i = start; i <= end; ++i) {
            curr = max((pre2 + nums[i]), pre1);
            pre2 = pre1;
            pre1 = curr;
        }
        return curr;
    }
};
```

## 4. 信件错排

题目描述：有 N 个 信 和 信封，它们被打乱，求错误装信方式的数量（所有信封都没有装各自的信）。

定义一个数组 dp 存储错误方式数量，dp[i] 表示前 i 个信和信封的错误方式数量。假设第 i 个信装到第 j 个信封里面，而第 j 个信装到第 k 个信封里面。根据 i 和 k 是否相等，有两种情况：

- i==k，交换 i 和 j 的信后，它们的信和信封在正确的位置，但是其余 i-2 封信有 dp[i-2] 种错误装信的方式。由于 j 有 i-1 种取值，因此共有 (i-1)\*dp[i-2] 种错误装信方式。
- i != k，交换 i 和 j 的信后，第 i 个信和信封在正确的位置，其余 i-1 封信有 dp[i-1] 种错误装信方式。由于 j 有 i-1 种取值，因此共有 (i-1)\*dp[i-1] 种错误装信方式。

综上所述，错误装信数量方式数量为：

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/da1f96b9-fd4d-44ca-8925-fb14c5733388.png" width="350px"> </div><br>

## 5. 母牛生产

[程序员代码面试指南-P181](#)

题目描述：假设农场中成熟的母牛每年都会生 1 头小母牛，并且永远不会死。第一年有 1 只小母牛，从第二年开始，母牛开始生小母牛。每只小母牛 3 年之后成熟又可以生小母牛。给定整数 N，求 N 年后牛的数量。

第 i 年成熟的牛的数量为：

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/879814ee-48b5-4bcb-86f5-dcc400cb81ad.png" width="250px"> </div><br>

# 矩阵路径

## 1. 矩阵的最小路径和

64\. Minimum Path Sum (Medium)

[Leetcode](https://leetcode.com/problems/minimum-path-sum/description/) / [力扣](https://leetcode-cn.com/problems/minimum-path-sum/description/)

```html
[[1,3,1],
 [1,5,1],
 [4,2,1]]
Given the above grid map, return 7. Because the path 1→3→1→1→1 minimizes the sum.
```

题目描述：求从矩阵的左上角到右下角的最小路径和，每次只能向右和向下移动。

**[题解](https://leetcode-cn.com/problems/minimum-path-sum/solution/zui-xiao-lu-jing-he-dong-tai-gui-hua-gui-fan-liu-c/)**：

- 状态定义：设 dp 为大小 m*n的矩阵，其中 `dp[i][j]`的值代表直到走到 (i, j) 的最小路径和。
- 转移方程：`dp[i][j] = min(dp[i - 1][j], dp[i][j - 1]) + grid[i][j]`

- 初始状态：dp初始化即可，不需要修改初始 0 值。
- 返回值：返回 dp 矩阵右下角值，即走到终点的最小路径和。

方法1：二维动态规划

```C++
class Solution1 {
public:
    int minPathSum(vector<vector<int>>& grid) {
        if (grid.empty() || grid[0].empty()) {
            return 0;
        }
        int m = grid.size(), n = grid[0].size();
        vector<vector<int>> dp(m, vector<int>(n, 0));
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if(i == 0 && j == 0) {
                    dp[i][j] = dp[i][j];        // 起点
                } else if (j == 0) {
                    dp[i][j] = dp[i - 1][j];    // 只能从上侧走到该位置
                } else if (i == 0) {
                    dp[i][j] = dp[i][j - 1];    // 只能从左侧走到该位置
                } else {
                    dp[i][j] = min(dp[i][j - 1], dp[i - 1][j]);
                }
                dp[i][j] += grid[i][j];
            }
        }
        return dp[m - 1][n - 1];
    }
};
```

方法2：一维动态规划

```C++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        if (grid.empty() || grid[0].empty()) {
            return 0;
        }
        int m = grid.size(), n = grid[0].size();
        vector<int> dp(n, 0);
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (j == 0) {
                    dp[j] = dp[j];        // 只能从上侧走到该位置
                } else if (i == 0) {
                    dp[j] = dp[j - 1];    // 只能从左侧走到该位置
                } else {
                    dp[j] = min(dp[j - 1], dp[j]);
                }
                dp[j] += grid[i][j];
            }
        }
        return dp[n - 1];
    }
};
```



## 2. 矩阵的总路径数

62\. Unique Paths (Medium)

[Leetcode](https://leetcode.com/problems/unique-paths/description/) / [力扣](https://leetcode-cn.com/problems/unique-paths/description/)

题目描述：统计从矩阵左上角到右下角的路径总数，每次只能向右或者向下移动。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/dc82f0f3-c1d4-4ac8-90ac-d5b32a9bd75a.jpg" width=""> </div><br>

**[题解](https://leetcode-cn.com/problems/unique-paths/solution/dong-tai-gui-hua-by-powcai-2/)**：

- 状态定义：设 dp 为大小 m*n的矩阵，其中 `dp[i][j]`的值代表走到 (i, j) 的路径数。
- 转移方程：`dp[i][j] = dp[i - 1][j] + dp[i][j - 1]`

- 初始状态：对于第一行 `dp[0][j]`，或者第一列 `dp[i][0]`，由于都是在边界，所以只能为 `1`
- 返回值：返回 dp 矩阵右下角值，即走到右下角的路径数。

```java
class Solution {
public:
    int uniquePaths(int m, int n) {
        if(m < 1 || n < 1)
            return 0;
        vector<int> dp(n, 1);
        for(int i = 1; i < m; ++i) {
            for(int j = 1; j < n; ++j) {
                dp[j] = dp[j] + dp[j - 1];
            }
        }
        return dp[n - 1];
    }
};
```

也可以直接用数学公式求解，这是一个组合问题。机器人总共移动的次数 S=m+n-2，向下移动的次数 D=m-1，那么问题可以看成从 S 中取出 D 个位置的组合数量，这个问题的解为 C(S, D)。

```C++
int uniquePaths(int m, int n) {
    int S = m + n - 2;  // 总共的移动次数
    int D = m - 1;      // 向下的移动次数
    long ret = 1;
    for (int i = 1; i <= D; i++) {
        ret = ret * (S - D + i) / i;
    }
    return (int) ret;
}
```



63. Unique Paths II (Medium)

[力扣](https://leetcode-cn.com/problems/unique-paths-ii/)

题目描述：考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？

**[题解](https://leetcode-cn.com/problems/unique-paths-ii/solution/bu-tong-lu-jing-ii-by-leetcode/)**：相比上一题的区别在于障碍，因而初始化时就要考虑第一行和第一列中是否有障碍物。若某一位置有障碍物，则此位置的路径长度置为0，以免对后面的路径产生贡献。

```C++
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        if(obstacleGrid.empty() || obstacleGrid[0].empty())
            return 0;
        if(obstacleGrid[0][0] == 1) // 起点即为障碍
            return 0;
        int r = obstacleGrid.size(), c = obstacleGrid[0].size();

        vector<vector<long long>> dp(r, vector<long long>(c, 0));
        dp[0][0] = 1; // 起点路径长度设为1
        for(int i = 0, j = 1; j < c; ++j) { // 初始化第一行
            if(obstacleGrid[i][j] == 1 || dp[i][j - 1] == 0)
                dp[i][j] = 0;
            else dp[i][j] = 1;
        }
        for(int i = 1, j = 0; i < r; ++i) { // 初始化第一列
            if(obstacleGrid[i][j] == 1 || dp[i - 1][j] == 0)
                dp[i][j] = 0;
            else dp[i][j] = 1;
        }

        for(int i = 1; i < r; ++i) {
            for(int j = 1; j < c; ++j) {
                if(obstacleGrid[i][j] == 1) // 有障碍，路径长度置为0，以免影响后面的
                    dp[i][j] = 0;
                else dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }
        return dp[r - 1][c - 1];
    }
};
```



# 数组区间

## 1. 数组区间和

303\. Range Sum Query - Immutable (Easy)

[Leetcode](https://leetcode.com/problems/range-sum-query-immutable/description/) / [力扣](https://leetcode-cn.com/problems/range-sum-query-immutable/description/)

```html
Given nums = [-2, 0, 3, -5, 2, -1]

sumRange(0, 2) -> 1
sumRange(2, 5) -> -1
sumRange(0, 5) -> -3
```

求区间 i \~ j 的和，可以转换为 sum[j + 1] - sum[i]，其中 sum[i] 为 0 \~ i - 1 的和。

```java
class NumArray {

    private int[] sums;

    public NumArray(int[] nums) {
        sums = new int[nums.length + 1];
        for (int i = 1; i <= nums.length; i++) {
            sums[i] = sums[i - 1] + nums[i - 1];
        }
    }

    public int sumRange(int i, int j) {
        return sums[j + 1] - sums[i];
    }
}
```

## 2. 数组中等差递增子区间的个数

413\. Arithmetic Slices (Medium)

[Leetcode](https://leetcode.com/problems/arithmetic-slices/description/) / [力扣](https://leetcode-cn.com/problems/arithmetic-slices/description/)

```html
A = [0, 1, 2, 3, 4]

return: 6, for 3 arithmetic slices in A:

[0, 1, 2],
[1, 2, 3],
[0, 1, 2, 3],
[0, 1, 2, 3, 4],
[ 1, 2, 3, 4],
[2, 3, 4]
```

dp[i] 表示以 A[i] 为结尾的等差递增子区间的个数。

当 A[i] - A[i-1] == A[i-1] - A[i-2]，那么 [A[i-2], A[i-1], A[i]] 构成一个等差递增子区间。而且在以 A[i-1] 为结尾的递增子区间的后面再加上一个 A[i]，一样可以构成新的递增子区间。

```html
dp[2] = 1
    [0, 1, 2]
dp[3] = dp[2] + 1 = 2
    [0, 1, 2, 3], // [0, 1, 2] 之后加一个 3
    [1, 2, 3]     // 新的递增子区间
dp[4] = dp[3] + 1 = 3
    [0, 1, 2, 3, 4], // [0, 1, 2, 3] 之后加一个 4
    [1, 2, 3, 4],    // [1, 2, 3] 之后加一个 4
    [2, 3, 4]        // 新的递增子区间
```

综上，在 A[i] - A[i-1] == A[i-1] - A[i-2] 时，dp[i] = dp[i-1] + 1。

因为递增子区间不一定以最后一个元素为结尾，可以是任意一个元素结尾，因此需要返回 dp 数组累加的结果。

```java
public int numberOfArithmeticSlices(int[] A) {
    if (A == null || A.length == 0) {
        return 0;
    }
    int n = A.length;
    int[] dp = new int[n];
    for (int i = 2; i < n; i++) {
        if (A[i] - A[i - 1] == A[i - 1] - A[i - 2]) {
            dp[i] = dp[i - 1] + 1;
        }
    }
    int total = 0;
    for (int cnt : dp) {
        total += cnt;
    }
    return total;
}
```

# 分割整数

## 1. 分割整数的最大乘积

343\. Integer Break (Medim)

[Leetcode](https://leetcode.com/problems/integer-break/description/) / [力扣](https://leetcode-cn.com/problems/integer-break/description/)

题目描述：For example, given n = 2, return 1 (2 = 1 + 1); given n = 10, return 36 (10 = 3 + 3 + 4).

```java
public int integerBreak(int n) {
    int[] dp = new int[n + 1];
    dp[1] = 1;
    for (int i = 2; i <= n; i++) {
        for (int j = 1; j <= i - 1; j++) {
            dp[i] = Math.max(dp[i], Math.max(j * dp[i - j], j * (i - j)));
        }
    }
    return dp[n];
}
```

## 2. 按平方数来分割整数

279\. Perfect Squares(Medium)

[Leetcode](https://leetcode.com/problems/perfect-squares/description/) / [力扣](https://leetcode-cn.com/problems/perfect-squares/description/)

题目描述：For example, given n = 12, return 3 because 12 = 4 + 4 + 4; given n = 13, return 2 because 13 = 4 + 9.

```java
public int numSquares(int n) {
    List<Integer> squareList = generateSquareList(n);
    int[] dp = new int[n + 1];
    for (int i = 1; i <= n; i++) {
        int min = Integer.MAX_VALUE;
        for (int square : squareList) {
            if (square > i) {
                break;
            }
            min = Math.min(min, dp[i - square] + 1);
        }
        dp[i] = min;
    }
    return dp[n];
}

private List<Integer> generateSquareList(int n) {
    List<Integer> squareList = new ArrayList<>();
    int diff = 3;
    int square = 1;
    while (square <= n) {
        squareList.add(square);
        square += diff;
        diff += 2;
    }
    return squareList;
}
```

## 3. 分割整数构成字母字符串

91\. Decode Ways (Medium)

[Leetcode](https://leetcode.com/problems/decode-ways/description/) / [力扣](https://leetcode-cn.com/problems/decode-ways/description/)

题目描述：Given encoded message "12", it could be decoded as "AB" (1 2) or "L" (12).

```java
public int numDecodings(String s) {
    if (s == null || s.length() == 0) {
        return 0;
    }
    int n = s.length();
    int[] dp = new int[n + 1];
    dp[0] = 1;
    dp[1] = s.charAt(0) == '0' ? 0 : 1;
    for (int i = 2; i <= n; i++) {
        int one = Integer.valueOf(s.substring(i - 1, i));
        if (one != 0) {
            dp[i] += dp[i - 1];
        }
        if (s.charAt(i - 2) == '0') {
            continue;
        }
        int two = Integer.valueOf(s.substring(i - 2, i));
        if (two <= 26) {
            dp[i] += dp[i - 2];
        }
    }
    return dp[n];
}
```

# 最长递增子序列

已知一个序列 {S<sub>1</sub>, S<sub>2</sub>,...,S<sub>n</sub>}，取出若干数组成新的序列 {S<sub>i1</sub>, S<sub>i2</sub>,..., S<sub>im</sub>}，其中 i1、i2 ... im 保持递增，即新序列中各个数仍然保持原数列中的先后顺序，称新序列为原序列的一个  **子序列**  。

如果在子序列中，当下标 ix > iy 时，S<sub>ix</sub> > S<sub>iy</sub>，称子序列为原序列的一个  **递增子序列**  。

定义一个数组 dp 存储最长递增子序列的长度，dp[n] 表示以 S<sub>n</sub> 结尾的序列的最长递增子序列长度。对于一个递增子序列 {S<sub>i1</sub>, S<sub>i2</sub>,...,S<sub>im</sub>}，如果 im < n 并且 S<sub>im</sub> < S<sub>n</sub>，此时 {S<sub>i1</sub>, S<sub>i2</sub>,..., S<sub>im</sub>, S<sub>n</sub>} 为一个递增子序列，递增子序列的长度增加 1。满足上述条件的递增子序列中，长度最长的那个递增子序列就是要找的，在长度最长的递增子序列上加上 S<sub>n</sub> 就构成了以 S<sub>n</sub> 为结尾的最长递增子序列。因此 dp[n] = max{ dp[i]+1 | S<sub>i</sub> < S<sub>n</sub> && i < n} 。

因为在求 dp[n] 时可能无法找到一个满足条件的递增子序列，此时 {S<sub>n</sub>} 就构成了递增子序列，需要对前面的求解方程做修改，令 dp[n] 最小为 1，即：

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/ee994da4-0fc7-443d-ac56-c08caf00a204.jpg" width="350px"> </div><br>

对于一个长度为 N 的序列，最长递增子序列并不一定会以 S<sub>N</sub> 为结尾，因此 dp[N] 不是序列的最长递增子序列的长度，需要遍历 dp 数组找出最大值才是所要的结果，max{ dp[i] | 1 <= i <= N} 即为所求。

## 1. 最长递增子序列

300\. Longest Increasing Subsequence (Medium)

[Leetcode](https://leetcode.com/problems/longest-increasing-subsequence/description/) / [力扣](https://leetcode-cn.com/problems/longest-increasing-subsequence/description/)

```java
public int lengthOfLIS(int[] nums) {
    int n = nums.length;
    int[] dp = new int[n];
    for (int i = 0; i < n; i++) {
        int max = 1;
        for (int j = 0; j < i; j++) {
            if (nums[i] > nums[j]) {
                max = Math.max(max, dp[j] + 1);
            }
        }
        dp[i] = max;
    }
    return Arrays.stream(dp).max().orElse(0);
}
```

使用 Stream 求最大值会导致运行时间过长，可以改成以下形式：

```java
int ret = 0;
for (int i = 0; i < n; i++) {
    ret = Math.max(ret, dp[i]);
}
return ret;
```

以上解法的时间复杂度为 O(N<sup>2</sup>)，可以使用二分查找将时间复杂度降低为 O(NlogN)。

定义一个 tails 数组，其中 tails[i] 存储长度为 i + 1 的最长递增子序列的最后一个元素。对于一个元素 x，

- 如果它大于 tails 数组所有的值，那么把它添加到 tails 后面，表示最长递增子序列长度加 1；
- 如果 tails[i-1] < x <= tails[i]，那么更新 tails[i] = x。

例如对于数组 [4,3,6,5]，有：

```html
tails      len      num
[]         0        4
[4]        1        3
[3]        1        6
[3,6]      2        5
[3,5]      2        null
```

可以看出 tails 数组保持有序，因此在查找 S<sub>i</sub> 位于 tails 数组的位置时就可以使用二分查找。

```java
public int lengthOfLIS(int[] nums) {
    int n = nums.length;
    int[] tails = new int[n];
    int len = 0;
    for (int num : nums) {
        int index = binarySearch(tails, len, num);
        tails[index] = num;
        if (index == len) {
            len++;
        }
    }
    return len;
}

private int binarySearch(int[] tails, int len, int key) {
    int l = 0, h = len;
    while (l < h) {
        int mid = l + (h - l) / 2;
        if (tails[mid] == key) {
            return mid;
        } else if (tails[mid] > key) {
            h = mid;
        } else {
            l = mid + 1;
        }
    }
    return l;
}
```

## 2. 一组整数对能够构成的最长链

646\. Maximum Length of Pair Chain (Medium)

[Leetcode](https://leetcode.com/problems/maximum-length-of-pair-chain/description/) / [力扣](https://leetcode-cn.com/problems/maximum-length-of-pair-chain/description/)

```html
Input: [[1,2], [2,3], [3,4]]
Output: 2
Explanation: The longest chain is [1,2] -> [3,4]
```

题目描述：对于 (a, b) 和 (c, d) ，如果 b < c，则它们可以构成一条链。

```java
public int findLongestChain(int[][] pairs) {
    if (pairs == null || pairs.length == 0) {
        return 0;
    }
    Arrays.sort(pairs, (a, b) -> (a[0] - b[0]));
    int n = pairs.length;
    int[] dp = new int[n];
    Arrays.fill(dp, 1);
    for (int i = 1; i < n; i++) {
        for (int j = 0; j < i; j++) {
            if (pairs[j][1] < pairs[i][0]) {
                dp[i] = Math.max(dp[i], dp[j] + 1);
            }
        }
    }
    return Arrays.stream(dp).max().orElse(0);
}
```

## 3. 最长摆动子序列

376\. Wiggle Subsequence (Medium)

[Leetcode](https://leetcode.com/problems/wiggle-subsequence/description/) / [力扣](https://leetcode-cn.com/problems/wiggle-subsequence/description/)

```html
Input: [1,7,4,9,2,5]
Output: 6
The entire sequence is a wiggle sequence.

Input: [1,17,5,10,13,15,10,5,16,8]
Output: 7
There are several subsequences that achieve this length. One is [1,17,10,13,10,16,8].

Input: [1,2,3,4,5,6,7,8,9]
Output: 2
```

要求：使用 O(N) 时间复杂度求解。

```java
public int wiggleMaxLength(int[] nums) {
    if (nums == null || nums.length == 0) {
        return 0;
    }
    int up = 1, down = 1;
    for (int i = 1; i < nums.length; i++) {
        if (nums[i] > nums[i - 1]) {
            up = down + 1;
        } else if (nums[i] < nums[i - 1]) {
            down = up + 1;
        }
    }
    return Math.max(up, down);
}
```

# 最长公共子序列

对于两个子序列 S1 和 S2，找出它们最长的公共子序列。

定义一个二维数组 dp 用来存储最长公共子序列的长度，其中 dp[i][j] 表示 S1 的前 i 个字符与 S2 的前 j 个字符最长公共子序列的长度。考虑 S1<sub>i</sub> 与 S2<sub>j</sub> 值是否相等，分为两种情况：

- 当 S1<sub>i</sub>==S2<sub>j</sub> 时，那么就能在 S1 的前 i-1 个字符与 S2 的前 j-1 个字符最长公共子序列的基础上再加上 S1<sub>i</sub> 这个值，最长公共子序列长度加 1，即 `dp[i][j] = dp[i-1][j-1] + 1`。
- 当 S1<sub>i</sub> != S2<sub>j</sub> 时，此时最长公共子序列为 S1 的前 i-1 个字符和 S2 的前 j 个字符最长公共子序列，或者 S1 的前 i 个字符和 S2 的前 j-1 个字符最长公共子序列，取它们的最大者，即 `dp[i][j] = max{ dp[i-1][j], dp[i][j-1] }`。

综上，最长公共子序列的状态转移方程为：

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/ecd89a22-c075-4716-8423-e0ba89230e9a.jpg" width="450px"> </div><br>

对于长度为 N 的序列 S<sub>1</sub> 和长度为 M 的序列 S<sub>2</sub>，`dp[N][M]` 就是序列 S<sub>1</sub> 和序列 S<sub>2</sub> 的最长公共子序列长度。

与最长递增子序列相比，最长公共子序列有以下不同点：

- 针对的是两个序列，求它们的最长公共子序列。
- 在最长递增子序列中，dp[i] 表示以 S<sub>i</sub> 为结尾的最长递增子序列长度，子序列必须包含 S<sub>i</sub> ；在最长公共子序列中，dp[i][j] 表示 S1 中前 i 个字符与 S2 中前 j 个字符的最长公共子序列长度，不一定包含 S1<sub>i</sub> 和 S2<sub>j</sub>。
- 在求最终解时，最长公共子序列中 `dp[N][M]` 就是最终解，而最长递增子序列中 dp[N] 不是最终解，因为以 S<sub>N</sub> 为结尾的最长递增子序列不一定是整个序列最长递增子序列，需要遍历一遍 dp 数组找到最大者。

## 1. 最长公共子序列

1143\. Longest Common Subsequence

[Leetcode](https://leetcode.com/problems/longest-common-subsequence/) / [力扣](https://leetcode-cn.com/problems/longest-common-subsequence/)

```java
    public int longestCommonSubsequence(String text1, String text2) {
        int n1 = text1.length(), n2 = text2.length();
        int[][] dp = new int[n1 + 1][n2 + 1];
        for (int i = 1; i <= n1; i++) {
            for (int j = 1; j <= n2; j++) {
                if (text1.charAt(i - 1) == text2.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        return dp[n1][n2];
    }
```



# 最长等差数列

1027.最长等差数列

[力扣](https://leetcode-cn.com/problems/longest-arithmetic-sequence/) 给定一个整数数组 `A`，返回 `A` 中最长等差子序列的长度。

`dp[i][diff]` 表示前0~i范围内，公差为diff的等差数列的长度，其中 `diff=A[i]-A[j]`，`n>i>j>=0`。则状态转移方程有两种情况：

1. `dp[j][diff]` 存在，又因为 `diff=A[i]-A[j]`，则在原始以 A[j] 为结尾的等差数列的后面可以添上 `A[i]`，有 `dp[i][diff] = dp[j][diff] + 1`。
2. 否则，则 A[i] 和 A[j] 两个数可以构成一个只有两个元素且偏移值为 diff 的等差数列，有 `dp[i][diff] = 2`。

方法1：使用哈希表来保存每一列，即每种公差对应的数列长度，无需对原数组进行排序，键为公差，值为长度

TODO：代码不完全正确，测试[1, 4, 2, 5, 3]时，输出应为5，而Leetcode预期结果为3，下面的代码输出也为3。牛客网的测试用例正确 - [牛客OJ](https://www.nowcoder.com/questionTerminal/4031f5a3723542e78a45b490c84c62b2)

```C++
class Solution {
public:
    int longestArithSeqLength(vector<int>& A) {
        int n = A.size();
        if(n <= 1) return n;
        vector<unordered_map<int, int>> dp(n);
        int res = 0, diff;
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < i; j++) {
                diff = A[i] - A[j];
                if(dp[j][diff]) dp[i][diff] = dp[j][diff] + 1;
                else dp[i][diff] = 2;
                res = max(res, dp[i][diff]);
            }
        }
        return res;
    }
};
```

方法2：使用vector来保存每一列，即每种公差对应的数列长度，需要先原数组进行排序（下面的解法未通过所有测试用例）

```C++
class Solution {
public:
    int longestArithSeqLength(vector<int>& nums) {
        int n = nums.size();
        sort(nums.begin(), nums.end());
        int len = nums[n - 1] - nums[0];
        if(len == 0) return n; // 所有元素相等

        vector<vector<int>> dp(n,vector<int>(len+1,1));
        int d, longest=1;
        for (int i = 1; i < n; i++){
            for (int j = i - 1; j >= 1; j--){
                d = nums[i] - nums[j];
                dp[i][d] = dp[j][d] + 1;
                longest = max(longest, dp[i][d]);
            }
        }

        return longest;
    }
};
```



# 0-1 背包

【[OJ](https://www.acwing.com/problem/content/description/2/)】有一个容量为 N 的背包，要用这个背包装下物品的价值最大（可以不恰好装满），这些物品有两个属性：重量 w 和价值 v。

定义一个二维数组 dp 存储最大价值，其中 **`dp[i][j]` 表示对于前 i 件物品，背包当前容量为 j 的情况下能装入的最大价值**。设第 i 件物品重量为 w，价值为 v，根据**第 i 件物品是否添加到背包中**，可以分两种情况讨论：

- 第 i 件物品没添加到背包，总重量不超过 j 的前 i 件物品的最大价值就是总重量不超过 j 的前 i-1 件物品的最大价值，`dp[i][j] = dp[i-1][j]`。
- 第 i 件物品添加到背包中，`dp[i][j] = dp[i-1][j-w] + v`。

第 i 件物品可添加也可以不添加，取决于哪种情况下最大价值更大。因此，0-1 背包的状态转移方程为：

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/8cb2be66-3d47-41ba-b55b-319fc68940d4.png" width="400px"> </div><br>

`dp[0][..] = dp[..][0] = 0`，因为没有物品或者背包没有空间的时候，能装的最大价值就是 0。**代码中从物品数量为1、背包容量为1开始枚举到物品数量为N、背包容量为W，最后结果为`dp[N][W]`。**

```C++
// W 为背包总重量
// N 为物品数量
// weights 数组存储 N 个物品的重量
// values 数组存储 N 个物品的价值
int knapsack(int W, int N, vector<int>& weights, vector<int>& values) {
    vector<vector<int>> dp(N + 1, vector<int>(W + 1, 0));
    for (int i = 1; i <= N; i++) { // 注意从物品数量为1开始枚举到N
        int w = weights[i - 1], v = values[i - 1]; // 第i个物品的重量，价值
        for (int j = 1; j <= W; j++) { // 注意从背包容量为1开始枚举到W
            if (j >= w) { // 第i个物体的重量不大于背包容量j，装入不装入择优
                dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - w] + v);
            } else { // 第i个物体的重量大于背包容量j，毫无疑问，不装入
                dp[i][j] = dp[i - 1][j];
            }
        }
    }
    return dp[N][W];
}
```

注意：第i件物品的价值为values[i - 1]而不是values[i]

**要求背包恰好装满**

- 如果并没有要求必须把背包装满，而是只希望价格尽量大，初始化时应该将dp数组所有元素设为0。

- 要求 “恰好装满背包” 时，dp数组初始化有些不同，初始化时要将`dp[0-N][0]`置为0，即第一列为0，其余置为负无穷。
  - 初始化的dp数组事实上就是在没有任何物品可以放入背包时的合法状态。如果要求背包恰好装满，那么此时只有容量为0的背包可能被重量为0的物品“恰好装满”，其它容量的背包均没有合法的解，属于未定义的状态，它们的值就都应该是-∞了。
  - 如果背包并非必须被装满，那么任何容量的背包都有一个合法解“什么都不装”，这个解的价值为0，所以初始时状态的值也就全部为0了。

将初始化语句改为：

```C++
        vector<vector<int>> dp(N + 1, vector<int>(W + 1, 0x80000000));
        for(int i = 0; i < N + 1; ++i)
            dp[i][0] = 0;
```

使用如下数据测试：

```
int N = 3, W = 4;
vector<int> weights = {2, 1, 3};
vector<int> values = {4, 2, 3};
```

结果：

```C++
// 不要求恰好装满
0 4 4 4
2 4 6 6
2 4 6 6
result = 6
// 要求恰好装满
-∞ 4 -∞ -∞
 2 4  6 -∞
 2 4  6  5
result = 5
```

**空间优化**  

在程序实现时可以对 0-1 背包做优化。观察状态转移方程可以知道，前 i 件物品的状态仅与前 i-1 件物品的状态有关，因此可以将 dp 定义为一维数组，其中 dp[j] 既可以表示 `dp[i-1][j]` 也可以表示 `dp[i][j]`。此时，

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/9ae89f16-7905-4a6f-88a2-874b4cac91f4.jpg" width="300px"> </div><br>

因为 `dp[j-w]` 表示 `dp[i-1][j-w]`，因此不能先求 `dp[i][j-w]`，防止将 `dp[i-1][j-w]` 覆盖。也就是说要先计算 `dp[i][j]` 再计算 `dp[i][j-w]`，在程序实现时需要按倒序来循环求解。

**此题与 “矩阵最小路径和“ 问题优化代码的不同**：

- 最小路径和的状态转移方程为：`dp[i][j] = min(dp[i - 1][j], dp[i][j - 1]) + grid[i][j]`，即当前值与上一行当前位置及**当前行左侧**位置的值有关，上一行当前位置的值可以再次使用，当前行左侧的值是刚算出来的，因而从左往右遍历并更新dp数组即可。
- 此题的状态转移方程为`dp[i][j] = max(dp[i-1][j], dp[i-1][j-w] + v)`，即当前值与上一行当前位置及**上一行左侧**位置的值有关，若当前行左侧位置的值先算，则上一行当前位置左边的值会被覆盖，所以要先算当前行右侧的值，即从右到左遍历更新dp数组。

```C++
int knapsack(int W, int N, vector<int>& weights, vector<int>& values) {
    vector<int> dp(W + 1, 0);
    for (int i = 1; i <= N; i++) {
        int w = weights[i - 1], v = values[i - 1];
        for (int j = W; j >= 1; j--) { // 逆序
            if (j >= w)
                dp[j] = max(dp[j], dp[j - w] + v);
        }
    }
    return dp[W];
}
```

注意：优化时，把行相关的内容全部删除

- 1、初始化一行多列：vector<int> dp(W + 1, 0)
- 2、删除状态转移方程中的i：dp[j] = max(dp[j], dp[j - w] + v);
- 3、仅返回最后一列：return dp[W];

**无法使用贪心算法的解释**  

0-1 背包问题无法使用贪心算法来求解，也就是说不能按照先添加性价比最高的物品来达到最优，这是因为这种方式可能造成背包空间的浪费，从而无法达到最优。考虑下面的物品和一个容量为 5 的背包，如果先添加物品 0 再添加物品 1，那么只能存放的价值为 16，浪费了大小为 2 的空间。最优的方式是存放物品 1 和物品 2，价值为 22.

| id | w | v | v/w |
| --- | --- | --- | --- |
| 0 | 1 | 6 | 6 |
| 1 | 2 | 10 | 5 |
| 2 | 3 | 12 | 4 |

------

**变种**  

- 完全背包：物品数量为无限个
- 多重背包：物品数量有限制
- 多维费用背包：物品不仅有重量，还有体积，同时考虑这两种限制
- 其它：物品之间相互约束或者依赖

资料：[背包九讲](https://blog.csdn.net/stack_queue/article/details/53544109)  [背包九讲专题视频](https://www.bilibili.com/video/av33930433/)



## 完全背包

【[OJ](https://www.acwing.com/problem/content/3/)】有一个容量为 N 的背包，要用这个背包装下物品的价值最大（可以不恰好装满），这些物品有两个属性：重量 w 和价值 v，并且每件**物品的数量不限**。

定义一个二维数组 dp 存储最大价值，其中 **`dp[i][j]` 表示对于前 i 件物品，背包当前容量为 j 的情况下能装入的最大价值**。设第 i 件物品重量为 w，价值为 v，可以分两种情况讨论：

- 第 i 件物品**不加选**到背包，总重量不超过 j 的前 i 件物品的最大价值就是总重量不超过 j 的前 i-1 件物品的最大价值，`dp[i][j] = dp[i-1][j]`。
- 第 i 件物品**加选**到背包中，`dp[i][j] = dp[i][j-w] + v`。

第 i 件物品可加选也可以不加选，取决于哪种情况下最大价值更大。因此，

**完全背包的状态转移方程**为： `dp[i][j] = max{dp[i-1][j], dp[i][j-w] + v}`

0-1背包问题的状态转移方程：`dp[i][j] = max{dp[i-1][j], dp[i-1][j-w] + v}`

可以看到，区别在于：

- 在0-1背包问题中，第i件物品放入背包的话，其状态从第i-1件物品的状态转移而来

  `dp[i][j] = dp[i-1][j-w] + v`

  **转移方向为**↘，即从上一行的左侧转移而来。

- 在完全背包问题中，第i件物品不加选的话，其状态从第i件物品重量少w的状态转移而来

  `dp[i][j] = dp[i][j-w] + v`

  **转移方向为**→，即从同一行的左侧转移而来。

空间优化的解法：

```C++
int knapsack(int W, int N, vector<int>& weights, vector<int>& values) {
    vector<int> dp(W + 1, 0);
    for (int i = 1; i <= N; i++) {
        int w = weights[i - 1], v = values[i - 1];
        for (int j = 1; j <= w; j++) { // 顺序
            if (j >= w)
                dp[j] = max(dp[j], dp[j - w] + v);
        }
    }
    return dp[W];
}
```

状态转移方程为：`dp[i][j] = max{dp[i-1][j], dp[i][j-w] + v}`，即当前值与上一行当前位置及**当前行左侧**位置的值有关，上一行当前位置的值可以再次使用，当前行左侧的值是刚算出来的，因而从左往右遍历并更新dp数组即可。

**总结：转移方向为**↘**的问题须使用逆序循环，转移方向为**→**的问题须使用顺序循环**



## 1.集合划分问题

### 1. 划分数组为和相等的两部分

416\. Partition Equal Subset Sum (Medium)

[Leetcode](https://leetcode.com/problems/partition-equal-subset-sum/description/) / [力扣](https://leetcode-cn.com/problems/partition-equal-subset-sum/description/)

```html
Input: [1, 5, 11, 5]

Output: true

Explanation: The array can be partitioned as [1, 5, 5] and [11].
```

可以看成一个背包大小为 sum/2 的 0-1 背包问题。

```java
public boolean canPartition(int[] nums) {
    int sum = computeArraySum(nums);
    if (sum % 2 != 0) {
        return false;
    }
    int W = sum / 2;
    boolean[] dp = new boolean[W + 1];
    dp[0] = true;
    for (int num : nums) {                 // 0-1 背包一个物品只能用一次
        for (int i = W; i >= num; i--) {   // 从后往前，先计算 dp[i] 再计算 dp[i-num]
            dp[i] = dp[i] || dp[i - num];
        }
    }
    return dp[W];
}

private int computeArraySum(int[] nums) {
    int sum = 0;
    for (int num : nums) {
        sum += num;
    }
    return sum;
}
```



### 2.划分数组使得两部分和的差最小

【[OJ](https://www.nowcoder.com/questionTerminal/2c05e084faa24efa92b2a36cbc6fd3dd)】给定一个数组，每个元素范围是0~K（K < 整数最大值2^32），将该数组分成两部分，使得 |S1- S2|最小，其中S1和S2分别是数组两部分的元素之和。输出|S1- S2|的值。

```
输入
5
2 4 5 6 9
输出
0
```

**题解**：这个问题可以转化为**求数组的一个子集，使得这个子集中的元素的和尽可能接近sum/2，其中sum为数组中所有元素的和**。这样转换之后这个问题就很类似0-1背包问题了：在n件物品中找到m件物品，他们的可以装入背包中，且总价值最大。不过这里不考虑价值，就考虑使得这些元素的和尽量接近sum/2，并且这里的和就相当于背包中的物品重量和。
定义`dp[i][j]`表示前i件物品中，总和最接近j的所有物品的总和，分两种情况：

1、第i件物品没有包括在其中 `dp[i][j] = dp[i-1][j]`

2、第i件物品包括在其中`dp[i][j] = dp[i-1][j-num]+num`

**状态转移方程**：`dp[i][j] = max{dp[i][j] = dp[i-1][j], dp[i-1][j-num]+num}`

从状态转移方程可以看到，这里加的是第i个数的值，相当于0-1背包问题中的重量，而0-1背包问题中加的是价值

题目要求返回的是量部分的差值，利用dp可求得第一部分和的最大值 x 并且 x<=sum/2，则第二部分的和为 sum-x，因而|S1-S2|=sum-x-x=sum-2*x

二维动规：`dp[i][j] = max(dp[i-1][j], dp[i-1][j-num] + num)`

```C++
vector<vector<int>> dp(n + 1, vector<int>(sum/2 + 1, 0));
for(int i = 1; i <= n; ++i) {
    int num = nums[i -1];
    for(int j = 1; j <= sum/2; ++j) {
        if(j >= num)
            dp[i][j] = max(dp[i-1][j], dp[i-1][j - num] + num);
        else dp[i][j] = dp[i-1][j];
    }
}
cout << sum - 2*dp[n][sum/2];
```

一维动规：`dp[j] = max(dp[j], dp[j-num] + num)`

```C++
vector<int> dp(sum/2 + 1, 0);
for(int i = 1; i <= n; ++i) {
    int num = nums[i -1];
    for(int j = sum/2; j >= 1; --j) {
        if(j >= num)
            dp[j] = max(dp[j], dp[j - num] + num);
    }
}
cout << sum - 2*dp[sum/2];
```



## 2. 改变一组数的正负号使得它们的和为一给定数

494\. Target Sum (Medium)

[Leetcode](https://leetcode.com/problems/target-sum/description/) / [力扣](https://leetcode-cn.com/problems/target-sum/description/)

```html
Input: nums is [1, 1, 1, 1, 1], S is 3.
Output: 5
Explanation:

-1+1+1+1+1 = 3
+1-1+1+1+1 = 3
+1+1-1+1+1 = 3
+1+1+1-1+1 = 3
+1+1+1+1-1 = 3

There are 5 ways to assign symbols to make the sum of nums be target 3.
```

该问题可以转换为 Subset Sum 问题，从而使用 0-1 背包的方法来求解。

可以将这组数看成两部分，P 和 N，其中 P 使用正号，N 使用负号，有以下推导：

```html
                  sum(P) - sum(N) = target
sum(P) + sum(N) + sum(P) - sum(N) = target + sum(P) + sum(N)
                       2 * sum(P) = target + sum(nums)
```

因此只要找到一个子集，令它们都取正号，并且和等于 (target + sum(nums))/2，就证明存在解。

```java
public int findTargetSumWays(int[] nums, int S) {
    int sum = computeArraySum(nums);
    if (sum < S || (sum + S) % 2 == 1) {
        return 0;
    }
    int W = (sum + S) / 2;
    int[] dp = new int[W + 1];
    dp[0] = 1;
    for (int num : nums) {
        for (int i = W; i >= num; i--) {
            dp[i] = dp[i] + dp[i - num];
        }
    }
    return dp[W];
}

private int computeArraySum(int[] nums) {
    int sum = 0;
    for (int num : nums) {
        sum += num;
    }
    return sum;
}
```

DFS 解法：

```java
public int findTargetSumWays(int[] nums, int S) {
    return findTargetSumWays(nums, 0, S);
}

private int findTargetSumWays(int[] nums, int start, int S) {
    if (start == nums.length) {
        return S == 0 ? 1 : 0;
    }
    return findTargetSumWays(nums, start + 1, S + nums[start])
            + findTargetSumWays(nums, start + 1, S - nums[start]);
}
```

## 3. 01 字符构成最多的字符串

474\. Ones and Zeroes (Medium)

[Leetcode](https://leetcode.com/problems/ones-and-zeroes/description/) / [力扣](https://leetcode-cn.com/problems/ones-and-zeroes/description/)

```html
Input: Array = {"10", "0001", "111001", "1", "0"}, m = 5, n = 3
Output: 4

Explanation: There are totally 4 strings can be formed by the using of 5 0s and 3 1s, which are "10","0001","1","0"
```

这是一个多维费用的 0-1 背包问题，有两个背包大小，0 的数量和 1 的数量。

```java
public int findMaxForm(String[] strs, int m, int n) {
    if (strs == null || strs.length == 0) {
        return 0;
    }
    int[][] dp = new int[m + 1][n + 1];
    for (String s : strs) {    // 每个字符串只能用一次
        int ones = 0, zeros = 0;
        for (char c : s.toCharArray()) {
            if (c == '0') {
                zeros++;
            } else {
                ones++;
            }
        }
        for (int i = m; i >= zeros; i--) {
            for (int j = n; j >= ones; j--) {
                dp[i][j] = Math.max(dp[i][j], dp[i - zeros][j - ones] + 1);
            }
        }
    }
    return dp[m][n];
}
```

## 4. 找零钱的最少硬币数

322\. Coin Change (Medium)

[Leetcode](https://leetcode.com/problems/coin-change/description/) / [力扣](https://leetcode-cn.com/problems/coin-change/description/)

```html
Example 1:
coins = [1, 2, 5], amount = 11
return 3 (11 = 5 + 5 + 1)

Example 2:
coins = [2], amount = 3
return -1.
```

题目描述：给一些面额的硬币，要求用这些硬币来组成给定面额的钱数，并且使得硬币数量最少。硬币可以重复使用。

- 物品：硬币
- 物品大小：面额
- 物品价值：数量

**题解**：因为硬币可以重复使用，因此这是一个完全背包问题。完全背包只需要将 0-1 背包的逆序遍历 dp 数组改为正序遍历即可。

子问题：扣除一个硬币后，所需硬币的最少数量

状态转移方程：

<img src="https://labuladong.github.io/ebook/pictures/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92%E8%AF%A6%E8%A7%A3%E8%BF%9B%E9%98%B6/coin.png" alt="img" style="zoom:67%;" />

子问题数目为 O(n)，处理一个子问题的时间为 O(k)，所以总的时间复杂度是 O(kn)

```C++
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        if(coins.empty()) return -1;
        vector<int> dp(amount+1, amount+1); // 数组大小为 amount+1，初始值也为 amount+1

        dp[0] = 0;
        for(int i = 1; i <= amount; ++i) {
            for(auto coin : coins) { // 求所有子问题+1的最小值
                if(i - coin < 0) continue; // 子问题无解，跳过
                dp[i] = min(dp[i], dp[i - coin] + 1);
            }
        }

        return dp[amount] == amount+1 ? -1 : dp[amount];
    }
};
```

## 5. 找零钱的硬币数组合

518\. Coin Change 2 (Medium)

[Leetcode](https://leetcode.com/problems/coin-change-2/description/) / [力扣](https://leetcode-cn.com/problems/coin-change-2/description/)

```text-html-basic
Input: amount = 5, coins = [1, 2, 5]
Output: 4
Explanation: there are four ways to make up the amount:
5=5
5=2+2+1
5=2+1+1+1
5=1+1+1+1+1
```

完全背包问题，使用 dp 记录可达成目标的组合数目。

```java
public int change(int amount, int[] coins) {
    if (coins == null) {
        return 0;
    }
    int[] dp = new int[amount + 1];
    dp[0] = 1;
    for (int coin : coins) {
        for (int i = coin; i <= amount; i++) {
            dp[i] += dp[i - coin];
        }
    }
    return dp[amount];
}
```

## 6. 字符串按单词列表分割

139\. Word Break (Medium)

[Leetcode](https://leetcode.com/problems/word-break/description/) / [力扣](https://leetcode-cn.com/problems/word-break/description/)

```html
s = "leetcode",
dict = ["leet", "code"].
Return true because "leetcode" can be segmented as "leet code".
```

dict 中的单词没有使用次数的限制，因此这是一个完全背包问题。

该问题涉及到字典中单词的使用顺序，也就是说物品必须按一定顺序放入背包中，例如下面的 dict 就不够组成字符串 "leetcode"：

```html
["lee", "tc", "cod"]
```

求解顺序的完全背包问题时，对物品的迭代应该放在最里层，对背包的迭代放在外层，只有这样才能让物品按一定顺序放入背包中。

```java
public boolean wordBreak(String s, List<String> wordDict) {
    int n = s.length();
    boolean[] dp = new boolean[n + 1];
    dp[0] = true;
    for (int i = 1; i <= n; i++) {
        for (String word : wordDict) {   // 对物品的迭代应该放在最里层
            int len = word.length();
            if (len <= i && word.equals(s.substring(i - len, i))) {
                dp[i] = dp[i] || dp[i - len];
            }
        }
    }
    return dp[n];
}
```

## 7. 组合总和

377\. Combination Sum IV (Medium)

[Leetcode](https://leetcode.com/problems/combination-sum-iv/description/) / [力扣](https://leetcode-cn.com/problems/combination-sum-iv/description/)

```html
nums = [1, 2, 3]
target = 4

The possible combination ways are:
(1, 1, 1, 1)
(1, 1, 2)
(1, 2, 1)
(1, 3)
(2, 1, 1)
(2, 2)
(3, 1)

Note that different sequences are counted as different combinations.

Therefore the output is 7.
```

涉及顺序的完全背包。

```java
public int combinationSum4(int[] nums, int target) {
    if (nums == null || nums.length == 0) {
        return 0;
    }
    int[] maximum = new int[target + 1];
    maximum[0] = 1;
    Arrays.sort(nums);
    for (int i = 1; i <= target; i++) {
        for (int j = 0; j < nums.length && nums[j] <= i; j++) {
            maximum[i] += maximum[i - nums[j]];
        }
    }
    return maximum[target];
}
```

# 股票交易

## 1. 需要冷却期的股票交易

309\. Best Time to Buy and Sell Stock with Cooldown(Medium)

[Leetcode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/description/) / [力扣](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/description/)

题目描述：交易之后需要有一天的冷却时间。


该题为马尔可夫过程，分为A观望，B持股，C冷却三个状态
状态转移图：A-(观望)->A, A-(买入｜-price)->B, B-(观望)->B, B-(卖出|+price)->C, C-(冷却)->A
可用维特比算法求解

```java
public int maxProfit(int[] prices) {
    if (prices == null || prices.length == 0) {
        return 0;
    }
    int N = prices.length;
    int[] A = new int[N];
    int[] B = new int[N];
    int[] C = new int[N];
    A[0] = 0;
    B[0] = C[0] = -prices[0];
    for (int i = 1; i < N; i++) {
        A[i] = Math.max(A[i - 1], C[i - 1]);
        B[i] = Math.max(B[i - 1], A[i - 1] - prices[i]);
        C[i] = B[i - 1] + prices[i];
    }
    return Math.max(A[N - 1], C[N - 1]);
}
```

## 2. 需要交易费用的股票交易

714\. Best Time to Buy and Sell Stock with Transaction Fee (Medium)

[Leetcode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/description/) / [力扣](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/description/)

```html
Input: prices = [1, 3, 2, 8, 4, 9], fee = 2
Output: 8
Explanation: The maximum profit can be achieved by:
Buying at prices[0] = 1
Selling at prices[3] = 8
Buying at prices[4] = 4
Selling at prices[5] = 9
The total profit is ((8 - 1) - 2) + ((9 - 4) - 2) = 8.
```

题目描述：每交易一次，都要支付一定的费用。


分为A观望，B持股，两个状态
状态转移图：A-(观望)->A, A-(买入|-price)->B, B-(观望)->B, B-(卖出|+price|-fee)->A

```java
public int maxProfit(int[] prices, int fee) {
    int N = prices.length;
    int[] A = new int[N];
    int[] B = new int[N];
    A[0] = 0;
    B[0] = -prices[0];
    for (int i = 1; i < N; i++) {
        A[i] = Math.max(A[i - 1], B[i - 1] + prices[i] -fee);
        B[i] = Math.max(A[i - 1] - prices[i], B[i - 1]);
    }
    return A[N - 1];
}
```


## 3. 只能进行两次的股票交易

123\. Best Time to Buy and Sell Stock III (Hard)

[Leetcode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/description/) / [力扣](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii/description/)

```java
public int maxProfit(int[] prices) {
    int firstBuy = Integer.MIN_VALUE, firstSell = 0;
    int secondBuy = Integer.MIN_VALUE, secondSell = 0;
    for (int curPrice : prices) {
        if (firstBuy < -curPrice) {
            firstBuy = -curPrice;
        }
        if (firstSell < firstBuy + curPrice) {
            firstSell = firstBuy + curPrice;
        }
        if (secondBuy < firstSell - curPrice) {
            secondBuy = firstSell - curPrice;
        }
        if (secondSell < secondBuy + curPrice) {
            secondSell = secondBuy + curPrice;
        }
    }
    return secondSell;
}
```

## 4. 只能进行 k 次的股票交易

188\. Best Time to Buy and Sell Stock IV (Hard)

[Leetcode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/description/) / [力扣](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv/description/)

```java
public int maxProfit(int k, int[] prices) {
    int n = prices.length;
    if (k >= n / 2) {   // 这种情况下该问题退化为普通的股票交易问题
        int maxProfit = 0;
        for (int i = 1; i < n; i++) {
            if (prices[i] > prices[i - 1]) {
                maxProfit += prices[i] - prices[i - 1];
            }
        }
        return maxProfit;
    }
    int[][] maxProfit = new int[k + 1][n];
    for (int i = 1; i <= k; i++) {
        int localMax = maxProfit[i - 1][0] - prices[0];
        for (int j = 1; j < n; j++) {
            maxProfit[i][j] = Math.max(maxProfit[i][j - 1], prices[j] + localMax);
            localMax = Math.max(localMax, maxProfit[i - 1][j] - prices[j]);
        }
    }
    return maxProfit[k][n - 1];
}
```

# 字符串编辑

## 1. 删除两个字符串的字符使它们相等

583\. Delete Operation for Two Strings (Medium)

[Leetcode](https://leetcode.com/problems/delete-operation-for-two-strings/description/) / [力扣](https://leetcode-cn.com/problems/delete-operation-for-two-strings/description/)

```html
Input: "sea", "eat"
Output: 2
Explanation: You need one step to make "sea" to "ea" and another step to make "eat" to "ea".
```

可以转换为求两个字符串的最长公共子序列问题。

```java
public int minDistance(String word1, String word2) {
    int m = word1.length(), n = word2.length();
    int[][] dp = new int[m + 1][n + 1];
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                dp[i][j] = dp[i - 1][j - 1] + 1;
            } else {
                dp[i][j] = Math.max(dp[i][j - 1], dp[i - 1][j]);
            }
        }
    }
    return m + n - 2 * dp[m][n];
}
```

## 2. 编辑距离

72\. Edit Distance (Hard)

[Leetcode](https://leetcode.com/problems/edit-distance/description/) / [力扣](https://leetcode-cn.com/problems/edit-distance/description/)

```html
Example 1:

Input: word1 = "horse", word2 = "ros"
Output: 3
Explanation:
horse -> rorse (replace 'h' with 'r')
rorse -> rose (remove 'r')
rose -> ros (remove 'e')
Example 2:

Input: word1 = "intention", word2 = "execution"
Output: 5
Explanation:
intention -> inention (remove 't')
inention -> enention (replace 'i' with 'e')
enention -> exention (replace 'n' with 'x')
exention -> exection (replace 'n' with 'c')
exection -> execution (insert 'u')
```

题目描述：修改一个字符串成为另一个字符串，使得修改次数最少。一次修改操作包括：插入一个字符、删除一个字符、替换一个字符。

```java
public int minDistance(String word1, String word2) {
    if (word1 == null || word2 == null) {
        return 0;
    }
    int m = word1.length(), n = word2.length();
    int[][] dp = new int[m + 1][n + 1];
    for (int i = 1; i <= m; i++) {
        dp[i][0] = i;
    }
    for (int i = 1; i <= n; i++) {
        dp[0][i] = i;
    }
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                dp[i][j] = dp[i - 1][j - 1];
            } else {
                dp[i][j] = Math.min(dp[i - 1][j - 1], Math.min(dp[i][j - 1], dp[i - 1][j])) + 1;
            }
        }
    }
    return dp[m][n];
}
```

## 3. 复制粘贴字符

650\. 2 Keys Keyboard (Medium)

[Leetcode](https://leetcode.com/problems/2-keys-keyboard/description/) / [力扣](https://leetcode-cn.com/problems/2-keys-keyboard/description/)

题目描述：最开始只有一个字符 A，问需要多少次操作能够得到 n 个字符 A，每次操作可以复制当前所有的字符，或者粘贴。

```
Input: 3
Output: 3
Explanation:
Intitally, we have one character 'A'.
In step 1, we use Copy All operation.
In step 2, we use Paste operation to get 'AA'.
In step 3, we use Paste operation to get 'AAA'.
```

```java
public int minSteps(int n) {
    if (n == 1) return 0;
    for (int i = 2; i <= Math.sqrt(n); i++) {
        if (n % i == 0) return i + minSteps(n / i);
    }
    return n;
}
```

```java
public int minSteps(int n) {
    int[] dp = new int[n + 1];
    int h = (int) Math.sqrt(n);
    for (int i = 2; i <= n; i++) {
        dp[i] = i;
        for (int j = 2; j <= h; j++) {
            if (i % j == 0) {
                dp[i] = dp[j] + dp[i / j];
                break;
            }
        }
    }
    return dp[n];
}
```

