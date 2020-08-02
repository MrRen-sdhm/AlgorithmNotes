# 贪心思想✏️🥇⭐️❌

贪心思想：保证每次操作都是局部最优的，并且最后得到的结果是全局最优的。



# 1. 分配饼干⭐️

455\. Assign Cookies (Easy)

[Leetcode](https://leetcode.com/problems/assign-cookies/description/) / [力扣](https://leetcode-cn.com/problems/assign-cookies/description/)

```html
Input: grid[1,3], size[1,2,4]
Output: 2
```

题目描述：每个孩子都有一个满足度 grid，每个饼干都有一个大小 size，只有饼干的大小大于等于一个孩子的满足度，该孩子才会获得满足。求解最多可以获得满足的孩子数量。

**题解**：

1. 给一个孩子的饼干应当**尽量小**并且又能满足该孩子，这样大饼干才能拿来给满足度比较大的孩子。
2. 因为满足度最小的孩子最容易得到满足，所以**先满足满足度最小的孩子**。

在以上的解法中，我们只在每次分配时饼干时选择一种看起来是当前最优的分配方法，但无法保证这种局部最优的分配方法最后能得到全局最优解。我们假设能得到全局最优解，并使用反证法进行证明，即假设存在一种比我们使用的贪心策略更优的最优策略。如果不存在这种最优策略，表示贪心策略就是最优策略，得到的解也就是全局最优解。

证明：假设在某次选择中，贪心策略选择给当前满足度最小的孩子分配第 m 个饼干，第 m 个饼干为可以满足该孩子的最小饼干。假设存在一种最优策略，可以给该孩子分配第 n 个饼干，并且 m < n。我们可以发现，经过这一轮分配，贪心策略分配后剩下的饼干一定有一个比最优策略来得大。因此在后续的分配中，贪心策略一定能满足更多的孩子。也就是说不存在比贪心策略更优的策略，即贪心策略就是最优策略。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/e69537d2-a016-4676-b169-9ea17eeb9037.gif" width="430px"> </div><br>

```cpp
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        if(g.empty() || s.empty()) return 0;
        int gi = 0, si = 0;
        sort(g.begin(), g.end());
        sort(s.begin(), s.end());
        while(gi < g.size() && si < s.size()) {
            if(g[gi] <= s[si]) { // 若这个较小的饼干能够满足该孩子
                gi++; // 将饼干分给该孩子
            }
            si++;
        }
        return gi;
    }
};
```



# 2. 不重叠的区间个数⭐️

435\. Non-overlapping Intervals (Medium)

[Leetcode](https://leetcode.com/problems/non-overlapping-intervals/description/) / [力扣](https://leetcode-cn.com/problems/non-overlapping-intervals/description/)

```html
Input: [ [1,2], [1,2], [1,2] ]

Output: 2

Explanation: You need to remove two [1,2] to make the rest of intervals non-overlapping.
```

```html
Input: [ [1,2], [2,3] ]

Output: 0

Explanation: You don't need to remove any of the intervals since they're already non-overlapping.
```

题目描述：计算让一组区间不重叠所需要移除的区间个数。

**题解**：

先计算最多能组成的不重叠区间个数，然后用区间总个数减去不重叠区间的个数。

在每次选择中，区间的结尾最为重要，选择的区间结尾越小，留给后面的区间的空间越大，那么后面能够选择的区间个数也就越大。

**按区间的结尾进行排序，每次选择结尾最小，并且和前一个区间不重叠的区间。**

```cpp
class Solution {
public:
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        if(intervals.empty()) return 0;
        sort(intervals.begin(), intervals.end(), // 按区间结尾从小到大排序 
            [](vector<int> &a, vector<int> &b) {return a[1] < b[1];});
        int cnt = 1; // 不重叠区间的个数
        int end = intervals[0][1]; // 区间结尾
        for(int i = 1; i < intervals.size(); i++) {
            if(intervals[i][0] < end) continue; // 跳过与当前区间重叠的区间
            end = intervals[i][1]; // 更新区间结尾
            cnt++; // 不重叠区间数加1
        }
        return intervals.size() - cnt; // 区间总数减不重叠区间数即为要移除的区间数
    }
};
```



# 3. 投飞镖刺破气球

452\. Minimum Number of Arrows to Burst Balloons (Medium)

[Leetcode](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/description/) / [力扣](https://leetcode-cn.com/problems/minimum-number-of-arrows-to-burst-balloons/description/)

```
Input:
[[10,16], [2,8], [1,6], [7,12]]

Output:
2
```

题目描述：气球在一个水平数轴上摆放，可以重叠，飞镖垂直投向坐标轴，使得路径上的气球都被刺破。求解最小的投飞镖次数使所有气球都被刺破。

**题解**：

也是计算不重叠的区间个数，不过和 Non-overlapping Intervals 的区别在于，[1, 2] 和 [2, 3] 在本题中算是重叠区间。

```cpp
class Solution {
public:
    int findMinArrowShots(vector<vector<int>>& points) {
        if(points.empty()) return 0;
        sort(points.begin(), points.end(), // 按区间结尾从小到大排序 
            [](vector<int> &a, vector<int> &b) {return a[1] < b[1];});
        int cnt = 1; // 不重叠区间的个数
        int end = points[0][1]; // 区间结尾
        for(int i = 1; i < points.size(); i++) {
            if(points[i][0] <= end) continue; // 跳过与当前区间重叠的区间
            end = points[i][1]; // 更新区间结尾
            cnt++;
        }
        return cnt;
    }
};
```



# 4. 根据身高和序号重组队列

406\. Queue Reconstruction by Height(Medium)

[Leetcode](https://leetcode.com/problems/queue-reconstruction-by-height/description/) / [力扣](https://leetcode-cn.com/problems/queue-reconstruction-by-height/description/)

```html
Input:
[[7,0], [4,4], [7,1], [5,0], [6,1], [5,2]]

Output:
[[5,0], [7,0], [5,2], [6,1], [4,4], [7,1]]
```

题目描述：一个学生用两个分量 (h, k) 描述，h 表示身高，k 表示排在前面的有 k 个学生的身高比他高或者和他一样高。

**题解**：

为了使插入操作不影响后续的操作，身高较高的学生应该先做插入操作，否则身高较小的学生原先正确插入的第 k 个位置可能会变成第 k+1 个位置。

身高 h 降序，若身高相同则按个数 k 值升序，然后将某个学生插入队列的第 k 个位置中。

C++中 insert 函数每次在迭代器所在位置之前插入新的元素，可实现按照学生个数组织队列。



方法1：使用额外空间

```cpp
class Solution {
public:
    vector<vector<int>> reconstructQueue(vector<vector<int>>& people) {
        if(people.empty()) return {};
        //按身高降序排序，身高相同则按个数升序排序
        sort(people.begin(), people.end(), [](vector<int> &a, vector<int> &b) {
                return a[0] == b[0] ? a[1] < b[1] : b[0] < a[0];
            });
        vector<vector<int>> res;
        for (auto i : people) { // 将某个学生插入队列的第k个位置前
            res.insert(res.begin() + i[1], i);
        }
        return res;
    }
};
```



方法2：不使用额外空间，直接把位置不对的元素从原数组中删除，并加入到正确的位置上去。

```cpp
class Solution {
public:
    vector<vector<int>> reconstructQueue(vector<vector<int>>& people) {
        if(people.empty()) return {};
        //按身高降序排序，身高相同则按个数升序排序
        sort(people.begin(), people.end(), [](vector<int> &a, vector<int> &b) {
                return a[0] == b[0] ? a[1] < b[1] : b[0] < a[0];
            });
        for (int i = 0; i < people.size(); i++) {
            auto p = people[i];
            if (p[1] != i) { // 若位置不对
                people.erase(people.begin() + i); // 从原数组删除
                people.insert(people.begin() + p[1], p); // 插入正确的位置
            }
        }
        return people;
    }
};
```



# 5. 买卖股票最大的收益

121\. Best Time to Buy and Sell Stock (Easy)

[Leetcode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/) / [力扣](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/description/)

题目描述：一次股票交易包含买入和卖出，只进行一次交易，求最大收益。

题解：

只要记录前面的最小价格minprice，将这个最小价格作为买入价格，然后将当前的价格作为售出价格，那么我们在第 i 天卖出股票能得到的利润就是 prices[i] - minprice，再查看当前收益是不是最大收益。

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        if(n == 0) return 0;
        int res = 0, minval = INT_MAX;
        for(int i = 0; i < n; i++) {
            minval = min(minval, prices[i]);
            res = max(res, prices[i] - minval);
        }
        return res;
    }
};
```



# 6. 买卖股票的最大收益 II

122\. Best Time to Buy and Sell Stock II (Easy)

[Leetcode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/description/) / [力扣](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/description/)

题目描述：可以进行多次交易，多次交易之间不能交叉进行，可以进行多次交易。

**题解**：

由于可以无限次买入和卖出。我们都知道炒股想挣钱当然是低价买入高价抛出，那么这里我们只需要从第二天开始，**如果当前价格比之前价格高，则把差值加入利润中**，因为我们可以昨天买入，今日卖出，若明日价更高的话，还可以今日买入，明日再抛出。以此类推，遍历完整个数组后即可求得最大利润。

对于 [a, b, c, d]，如果有 a <= b <= c <= d ，那么最大收益为 d - a。而 d - a = (d - c) + (c - b) + (b - a) ，因此当访问到一个 prices[i] 且 prices[i] - prices[i-1] > 0，那么就把 prices[i] - prices[i-1] 添加到收益中。

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.empty()) return 0;
        int res = 0;
        for(int i = 1; i < prices.size(); i++) {
            if(prices[i] > prices[i - 1])
                res += prices[i] - prices[i - 1];
        }
        return res;
    }
};
```



# 7. 种植花朵⭐️

605\. Can Place Flowers (Easy)

[Leetcode](https://leetcode.com/problems/can-place-flowers/description/) / [力扣](https://leetcode-cn.com/problems/can-place-flowers/description/)

```html
Input: flowerbed = [1,0,0,0,1], n = 1
Output: True
```

题目描述：flowerbed 数组中 1 表示已经种下了花朵。花朵之间至少需要一个单位的间隔，求解是否能种下 n 朵花。

题解：

贪心：若数组中一个位置为，且其左右均为0， 那么此处可种花。首尾位置只判断一侧即可，即认为首位置之前为0，其后一位为0则可种花。末位置之后为0，其前一位为0即可种花。



方法1：遍历花床，如果某个位置为0，我们就看其前面一个和后面一个位置的值，注意处理首位置和末位置的情况（首位置之前处理为0，末位置之后处理为0），如果pre和next均为0，那么说明当前位置可以放花，我们修改flowerbed的值，并且n自减1，最后看n是否小于等于0。

```cpp
class Solution {
public:
    bool canPlaceFlowers(vector<int>& flowerbed, int n) {
        for(int i = 0; i < flowerbed.size(); i++) {
            if(flowerbed[i] == 0) { // 当前位置未种花
                int pre = (i == 0 ? 0 : flowerbed[i - 1]); // 前一位置是否种了花
                int next = (i == flowerbed.size() - 1 ? 0 : flowerbed[i + 1]); // 后一位置
                if(pre + next == 0) { // 前后均未种花
                    n--; // 剩余花的数量减1
                    flowerbed[i] = 1; // 将当前位置种上花 
                }
            }
        }
        return n <= 0;
    }
};
```



方法2：为了不特殊处理首末位置，直接先在首尾各加了一个0，然后就三个三个的来遍历，如果找到了三个连续的0，那么n自减1，i自增1，这样相当于i一下向后跨了两步。

```cpp
class Solution {
public:
    bool canPlaceFlowers(vector<int>& flowerbed, int n) {
        flowerbed.insert(flowerbed.begin(), 0); // 首位之前插入0
        flowerbed.push_back(0); // 末位之后插入0
        for(int i = 1; i < flowerbed.size() - 1; i++) {
            if(flowerbed[i - 1] + flowerbed[i] + flowerbed[i + 1] == 0) { // 连续3个0
                n--; // 剩余花的数量减1
                i++; // 跳过下一位置
            }
        }
        return n <= 0;
    }
};
```



# 8. 分隔字符串使同种字符出现在一起⭐️

763\. Partition Labels (Medium)

[Leetcode](https://leetcode.com/problems/partition-labels/description/) / [力扣](https://leetcode-cn.com/problems/partition-labels/description/)

```html
Input: S = "ababcbacadefegdehijhklij"
Output: [9,7,8]
Explanation:
The partition is "ababcbaca", "defegde", "hijhklij".
This is a partition so that each letter appears in at most one part.
A partition like "ababcbacadefegde", "hijhklij" is incorrect, because it splits S into less parts.
```

**题解**：

这道题的难点就是如何找到字符串的断点，即拆分成为子串的位置。我们仔细观察题目中的例子，可以发现**一旦某个字母多次出现了，那么其最后一个出现位置必须要在当前子串中**，字母 a，e，和 j，分别是三个子串中的结束字母。所以我们关注的是每个字母最后的出现位置，我们可以使用一个 HashMap 来建立字母和其最后出现位置之间的映射，那么对于题目中的例子来说，我们可以得到如下映射：

a -> 8
b -> 5
c -> 7
d -> 14
e -> 15
f -> 11
g -> 13
h -> 19
i -> 22
j -> 23
k -> 20
l -> 21

建立好映射之后，就需要开始遍历字符串S了，我们维护一个 start 变量，是当前子串的起始位置，还有一个 last 变量，是当前子串的结束位置，每当我们遍历到一个字母，我们需要在 HashMap 中提取出其最后一个位置，因为一旦当前子串包含了一个字母，其必须包含所有的相同字母，所以我们要**不停的用当前字母的最后一个位置来更新 last 变量**，只有当 i 和 last 相同了，即当 i = 8 时，当前子串包含了所有已出现过的字母的最后一个位置，即之后的字符串里不会有之前出现过的字母了，此时就应该是断开的位置，我们将长度9加入结果 res 中，同理类推，我们可以找出之后的断开的位置。

```cpp
class Solution {
public:
    vector<int> partitionLabels(string S) {
        if(S.empty()) return {};
        vector<int> res;
        int n = S.size(), start = 0, last = 0; // start指向当前子串开始位置，last指向结束位置
        unordered_map<char, int> m;
        for (int i = 0; i < n; ++i) m[S[i]] = i; // 保存字母最后一次出现的位置
        for (int i = 0; i < n; ++i) {
            last = max(last, m[S[i]]); // 用当前字母的最后位置更新last，使last指向整个子串的结束位置
            if (i == last) { // 若当前字符的位置即为整个子串的结束位置
                res.push_back(i - start + 1); // 保存当前子串的长度
                start = i + 1; // 更新下一子串的起始位置
            }
        }
        return res;
    }
};
```
