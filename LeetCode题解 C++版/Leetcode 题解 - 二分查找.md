# 二分查找✏️🥇⭐️❌

# 二分查找模板

## 模板1：整数二分模板

二分的目的：二分查找退出循环时mid指针指向目标元素

两种情况：

- 要找的数处于重复区段的最左侧。
  - 需将区间`[l, r]`划分为`[l, mid]`和`[mid + 1, r]`，从而找到重复元素区段最左侧元素所在位置
- 要找的数处于重复区段的最右侧
  - 需将区间[l, r]划分为`[l, mid - 1]`和`[mid, r]`，从而找到重复元素区段最右侧元素所在位置

注意

- 当区间被划分成`[l, mid - 1]`和`[mid, r]`时，由于除法向下取整的性质，若不进行特殊处理，则会出现无限循环。如`l = r - 1`时，若`mid = l + r >> 1`，则`mid = l`，会导致左边界一直无法更新，此时便需要将`mid`加1，即`mid = l + r + 1 >> 1`。
- 数组中不存在目标元素时，查找也能正常退出，但是最终`l`或`r`指向的元素并不等于目标数字
  - 模板bsearch_1会返回第一个比目标数字大的位置
  - 模板bsearch_1会返回第一个比目标数字的位置
- 循环退出后，`l`一定等于`r`，最终结果取`l`或者`r`没有区别

```cpp
bool check(int x) {/* ... */} // 检查x是否满足某种性质

// 区间[l, r]被划分成[l, mid]和[mid + 1, r]时使用：
int bsearch_1(int l, int r) {
    while (l < r) {
        int mid = l + r >> 1;
        if (check(mid)) r = mid;    // check()判断mid是否满足性质
        else l = mid + 1;
    }
    return l;
}
// 区间[l, r]被划分成[l, mid - 1]和[mid, r]时使用：
int bsearch_2(int l, int r) {
    while (l < r) {
        int mid = l + r + 1 >> 1;
        if (check(mid)) l = mid;
        else r = mid - 1;
    }
    return l;
}
```

使用方法：

```cpp
#include <iostream>

using namespace std;

const int N = 6;
int q[N] = {1, 2, 3, 3, 4, 5};
int aim = 3;

int main() {
    // 查找第一个重复元素
    int l = 0, r = N - 1;
    while(l < r) {
        int mid = l + r >> 1;
        if(q[mid] >= aim) r = mid; // 找到大于等于aim的值，立即抛弃右侧，特殊情况就是找到等于aim的值，那么必须要抛弃右侧以获取最左侧元素
        else l = mid + 1;
    }
    cout << l << " " << r << endl;
    if(q[l] != aim) cout << "not found" << endl;
    
    // 查找最后一个重复元素
    l = 0, r = N - 1;
    while(l < r) {
        int mid = l + r + 1 >> 1;
        if(q[mid] <= aim) l = mid; // 找到小于等于aim的值，立即抛弃左侧，特殊情况就是找到等于aim的值，那么必须要抛弃左侧以获取最右侧元素
        else r = mid - 1;
    }
    cout << l << " " << r << endl;
    if(q[l] != aim) cout << "not found" << endl;
    return 0;
}
```



## 模板2：浮点数二分模板

```cpp
bool check(double x) {/* ... */} // 检查x是否满足某种性质

double bsearch_3(double l, double r)
{
    const double eps = 1e-6;   // eps 表示精度，取决于题目对精度的要求
    while (r - l > eps) {
        double mid = (l + r) / 2;
        if (check(mid)) r = mid;
        else l = mid;
    }
    return l;
}
```

使用方法：

```cpp
#include <iostream>
using namespace std;

int main() { // 求二次方根
    int x; cin >> x;

    double l = 0, r = x;
    while(r - l > 1e-9) {
        double mid = (l + r) / 2;
        if(mid * mid  >= x) r = mid;
        else l = mid;
    }
    cout << l << endl;
    return 0;
}
```

------



# 浮点数二分

## 1. 求开方

69\. Sqrt(x) / x的平方根 (Easy)

[Leetcode](https://leetcode.com/problems/sqrtx/description/) / [力扣](https://leetcode-cn.com/problems/sqrtx/description/)

```html
Input: 4
Output: 2

Input: 8
Output: 2
Explanation: The square root of 8 is 2.82842..., and since we want to return an integer, the decimal part will be truncated.
```

一个数 x 的开方 sqrt 一定在 0 \~ x 之间，并且满足 sqrt * sqrt == x。可以利用二分查找在 0 \~ x 之间查找 sqrt。

对于 x = 8，它的开方是 2.82842...，最后应该返回 2 而不是 3。在循环条件为 `r - l > 1e-6` 并且循环退出时，r 总是比 l 小 1，也就是说 r = 2，l = 3，因此最后的返回值应该为 r 而不是 l。

```cpp
class Solution {
public:
    int mySqrt(int x) {
        double l = 0, r = x;
        while(r - l > 1e-6) {
            double mid = (l + r) / 2;
            if(mid * mid >= x) r = mid;
            else l = mid;
        }
        
        return r;
    }
};
```



# 正常二分

## 1. 排序数组中元素的首尾位置

[Leetcode 34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

题目描述：给定一个有序数组 nums 和一个目标 target，要求找到 target 在 nums 中的第一个位置和最后一个位置。

```html
Input: nums = [5,7,7,8,8,10], target = 8
Output: [3,4]

Input: nums = [5,7,7,8,8,10], target = 6
Output: [-1,-1]
```

**题解**：

方法1：使用上述两个模板即可轻松解决

```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        if(nums.empty()) return {-1, -1};
        int n = nums.size();
        vector<int> res;

        int l = 0, r = n - 1;
        while(l < r) {
            int mid = l + r >> 1;
            if(nums[mid] >= target) r = mid;
            else l = mid + 1;
        }

        if(nums[l] != target) {
            res = {-1, -1};  
        }
        else {
            res.push_back(l);

            l = 0, r = n - 1;
            while(l < r) {
                int mid = l + r + 1 >> 1;
                if(nums[mid] <= target) l = mid;
                else r = mid - 1;
            }

            res.push_back(l);
        }
        return res;
    }
};
```

方法2：可用二分查找找出第一个位置和最后一个位置，但是寻找的方法有所不同，需要实现两个二分查找。我们将寻找  target 最后一个位置，转换成寻找 target+1 第一个位置，再往前移动一个位置。这样我们只需要实现一个二分查找代码即可。

```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        if(nums.empty()) return {-1, -1};

        int first = findFirst(nums, target); // 查找第一个位置
        int last = findFirst(nums, target + 1); // 查找最后一个位置的下一个位置

        if(nums[first] != target) return {-1, -1}; // 第一个位置不存在，说明数组中不存在目标值

        // 最后一个位置的下一个位置存在，则最后一个位置为最后一个位置的下一个位置减去1
        if(nums[last] != target) last -= 1;

        return {first, last};
    }

    int findFirst(vector<int>& nums, int target) {
        int l = 0, r = nums.size() - 1;
        while(l < r) {
            int mid = l + (r - l >> 2);
            if(nums[mid] >= target) r = mid;
            else l = mid + 1;
        }
        return l;
    }
};
```



## 2. 大于给定元素的最小元素

744\. Find Smallest Letter Greater Than Target / 寻找比目标字母大的最小字母 (Easy)

[Leetcode](https://leetcode.com/problems/find-smallest-letter-greater-than-target/description/) / [力扣](https://leetcode-cn.com/problems/find-smallest-letter-greater-than-target/description/)

```html
Input:
letters = ["c", "f", "j"]
target = "d"
Output: "f"

Input:
letters = ["c", "f", "j"]
target = "k"
Output: "c"
```

题目描述：给定一个有序的字符数组 letters 和一个字符 target，要求找出 letters 中大于 target 的最小字符，如果找不到就返回第 1 个字符。

**题解**：

这里找**第一个大于目标值**的字符，因而检测条件为`letters[mid] > target`，且不加等号，若当前值大于目标值，并将 r 设为 mid 从而**切除当前值右侧更大的值**

```cpp
class Solution {
public:
    char nextGreatestLetter(vector<char>& letters, char target) {
        int n = letters.size();
        if(target >= letters.back() || target < letters[0]) // 处理特殊情况
            return letters[0];

        int l = 0, r = n - 1;
        while(l < r) {
            int mid = l + r >> 1;
            if(letters[mid] > target) r = mid; // 这里分界条件设为>目的为找到大于目标值的数
            else l = mid + 1;
        }

        return letters[l];
    }
};
```

若修改为**找第一个小于目标值**的字符，则检测条件为`letters[mid] < target`，且不加等号，即当前值小于目标值，并将 l 设为 mid 从而**切除当前值左侧更小的值**

```cpp
        int l = 0, r = n - 1;
        while(l < r) {
            int mid = l + r + 1 >> 1;
            if(letters[mid] < target) l = mid; // 这里分界条件设为<目的为找到小于目标值的数
            else r = mid - 1;
        }
```



## 3. 第一个错误的版本

278\. First Bad Version / 第一个错误的版本 (Easy)

[Leetcode](https://leetcode.com/problems/first-bad-version/description/) / [力扣](https://leetcode-cn.com/problems/first-bad-version/description/)

题目描述：给定一个元素 n 代表有 [1, 2, ..., n] 版本，在第 x 位置开始出现错误版本，导致后面的版本都错误。可以调用 isBadVersion(int x) 知道某个版本是否错误，要求找到第一个错误的版本。

**题解**：

如果第 mid 个版本出错，则表示第一个错误的版本在 [l, mid] 之间，令 l = mid；否则第一个错误的版本在 [mid + 1, r] 之间，令 l = mid + 1。

可以看出，区间划分为[l, mid] 和 [mid + 1, r] 因而使用二分模板1即可。

```cpp
public int firstBadVersion(int n) {
    int l = 1, h = n;
    while (l < h) {
        int mid = l + (h - l) / 2;
        if (isBadVersion(mid)) {
            h = mid;
        } else {
            l = mid + 1;
        }
    }
    return l;
}
```



## 4. 旋转数组的最小数字

153\. Find Minimum in Rotated Sorted Array (Medium)

[Leetcode](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/description/) / [力扣](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/description/)

```html
Input: [3,4,5,1,2],
Output: 1
```

**题解**：若数组没有旋转或者旋转点在左半段的时候，中间值是一定小于右边界值的，所以要去左半边继续搜索，反之则去右半段查找。

```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        int l = 0, r = nums.size() - 1;
        while(l < r) {
            int mid = l + r >> 1;
            if(nums[mid] <= nums[r]) r = mid;
            else l = mid + 1;
        }
        return nums[l];
    }
};
```



# 二分查找变形

## 1. 有序数组中只出现一次的数字⭐️

540\. Single Element in a Sorted Array / 有序数组中的单一元素 (Medium)

[Leetcode](https://leetcode.com/problems/single-element-in-a-sorted-array/description/) / [力扣](https://leetcode-cn.com/problems/single-element-in-a-sorted-array/description/)

```html
Input: [1, 1, 2, 3, 3, 4, 4, 8, 8]
Output: 2
```

题目描述：一个有序数组只有一个数不出现两次，找出这个数。

**题解**：

此题与 [Leetcode 136. 只出现一次的数字](https://leetcode-cn.com/problems/single-number/) 类似（题解见数组与矩阵部分），但此题已排序，并且限制不同。

要求以 O(logN) 时间复杂度进行求解，因此不能遍历数组并进行异或操作来求解，这么做的时间复杂度为 O(N)。



方法1：偶数位二分查找

令 index 为 Single Element 在数组中的位置。在 index 之后，数组中原来存在的成对状态被改变。如果 m 为偶数，并且 m + 1 < index，那么 nums[m] == nums[m + 1]；m + 1 >= index，那么 nums[m] != nums[m + 1]。

从上面的规律可以知道，如果 nums[m] == nums[m + 1]，那么 index 所在的数组位置为 [m + 2, h]，此时令 l = m + 2；如果 nums[m] != nums[m + 1]，那么 index 所在的数组位置为 [l, m]，此时令 h = m。

**核心**：保证查找区间中元素个数为奇数，这样限制后，若nums[mid] != nums[mid + 1]那么落单数一定在左侧否则一定在右侧。

```cpp
class Solution {
public:
    int singleNonDuplicate(vector<int>& nums) {
        int n = nums.size();
        int l = 0, r = n - 1;
        while(l < r) {
            int mid = l + r >> 1;
            if (mid % 2 == 1) --mid; // 保证 l/r/m 都在偶数位，使得查找区间大小一直都是奇数
            if(nums[mid] != nums[mid + 1]) r = mid;
            else l = mid + 2;
        }
        return nums[l];
    }
};
```



方法2：二分查找+异或运算

**技巧：异或1运算可以将坐标两两归为一对，比如0和1，2和3，4和5等等**。异或1可以直接找到一对中的另一个数字，比如对于2，亦或1就是3，对于3，亦或1就是2。如果你和你的小伙伴相等了，说明落单数在右边，如果不等，说明在左边。

```cpp
class Solution {
public:
    int singleNonDuplicate(vector<int>& nums) {
        int n = nums.size();
        int l = 0, r = n - 1;
        while(l < r) {
            int mid = l + r >> 1;
            if(nums[mid] != nums[mid^1]) r = mid;
            else l = mid + 1;
        }
        return nums[l];
    }
};
```



## 2. 寻找数组中重复的数⭐️

287\. Find the Duplicate Number / 寻找重复数 (Medium)

[Leetcode](https://leetcode.com/problems/find-the-duplicate-number/description/) / [力扣](https://leetcode-cn.com/problems/find-the-duplicate-number/description/)  给定一个包含 **n + 1 个整数**的数组 nums，其数字都在 1 到 n 之间（包括 1 和 n），可知至少存在一个重复的整数。假设**只有一个重复的整数**，找出这个重复的数。

要求不能修改数组，也不能使用额外的空间。

```
Input: [1,3,4,2,2]
Output: 2
```

**题解**：

[方法1](https://leetcode-cn.com/problems/find-the-duplicate-number/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--52/)：二分查找，**也适用于数组中存在多个重复的数**（可找到其中一个），时间复杂度**O(nlogn)**

此题不允许使用额外空间，也不允许修改原数组，因而无法排序。但是题中限定数据范围为`[1, n]`，而序列`1,2...,n`是有序的，因而可以**在`[1, n]`中进行二分查找，注意不是在nums数组中进行查找**。`mid = (1 + n) / 2`，接下来判断最终答案是在 `[1, mid]` 中还是在 `[mid + 1, n]` 中。

为了缩小区间，需要统计原数组中小于等于 `mid` 的元素个数，记为 `count`。如果 `count > mid` ，根据鸽巢原理，在 `[1,mid]` 范围内的数字个数超过了 `mid` ，所以区间中`[1, mid]`一定有一个重复数字，保留区间`[1, mid]`。否则重复元素在`[mid + 1, n]`中，切除区间`[mid + 1, n]`。

最终两个指针的值即为重复数字！

```cpp
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        int n = nums.size();

        int l = 1, r = n;
        while(l < r) {
            int mid = l + r >> 1;
			// 统计原数组中<=mid的元素个数
            int cnt = 0;
            for(int i = 0; i < n; i++)
                if(nums[i] <= mid) cnt++;
            
            if(cnt > mid) r = mid; // 重复数字在区间[1, mid]中
            else l = mid + 1; // 重复数字在区间[mid + 1, n]中
        }
        return l; // 最终两个指针的值即为重复数字
    }
};
```



[方法2](https://leetcode-cn.com/problems/find-the-duplicate-number/solution/kuai-man-zhi-zhen-de-jie-shi-cong-damien_undoxie-d/)：快慢指针，**不适用于数组中存在多个重复数字的情况**，时间复杂度**O(n)**

由于题目限定了区间 [1,n]，所以可以巧妙的利用坐标和数值之间相互转换，而由于重复数字的存在，那么一定会形成环，用快慢指针可以找到环并确定环的起始位置。

**可以把nums[i]想象成链表中的节点，nums[i]存储的是每个节点的next指针**。例如nums=[1,3,4,2,2]，则链表中的节点nums[0]=1指向下标为1的节点nums[1]=3，节点nums[1]=3指向下标为3的节点nums[3]=2，节点nums[3]=2指向下标为2的节点nums[2]=4，节点nums[2]=4指向下标为4的节点nums[4]=2，节点nums[4]=2指向下标为2的节点nums[2]=4，此时产生了环，指针的指向即为重复的元素2

<img src="C:/Users/sdhm/AppData/Roaming/Typora/typora-user-images/image-20200523171128636.png" alt="image-20200523171128636" style="zoom:67%;" />

```cpp
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        int fast = 0, slow = 0;
        while(true){ // 快指针每次走两步，慢指针走一步
            fast = nums[nums[fast]];
            slow = nums[slow];
            if(fast == slow) break;
        }
        int fast = 0; // 快指针从头开始走
        while(true){ // 两指针每次均走一步
            finder = nums[fast];
            slow = nums[slow];
            if(slow == fast) break;        
        }
        return slow;
    }
};
```

