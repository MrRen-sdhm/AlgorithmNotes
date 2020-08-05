# 哈希表✏️🥇⭐️❌

哈希表使用 O(N) 空间复杂度存储数据，并且以 O(1) 时间复杂度求解问题。

- C++中的   **unordered_set**   用于存储一个集合，可以查找元素是否在集合中。如果元素有穷，并且范围不大，那么可以用一个布尔数组来存储一个元素是否存在。例如对于只有小写字符的元素，就可以用一个长度为 26 的布尔数组来存储一个字符集合，使得空间复杂度降低为 O(1)。

- C++中的   **unordered_map**   主要用于映射关系，从而把两个元素联系起来。HashMap 也可以用来对元素进行计数统计，此时键为元素，值为计数。和 HashSet 类似，如果元素有穷并且范围不大，可以用整型数组来进行统计。在对一个内容进行压缩或者其它转换时，利用 HashMap 可以把原始内容和转换后的内容联系起来。例如在一个简化 url 的系统中 [Leetcdoe : 535. Encode and Decode TinyURL (Medium)

[Leetcode](https://leetcode.com/problems/encode-and-decode-tinyurl/description/)，利用 unordered_map 就可以存储精简后的 url 到原始 url 的映射，使得不仅可以显示简化的 url，也可以根据简化的 url 得到原始 url 从而定位到正确的资源) / [力扣](https://leetcode-cn.com/problems/encode-and-decode-tinyurl/description/)，利用 HashMap 就可以存储精简后的 url 到原始 url 的映射，使得不仅可以显示简化的 url，也可以根据简化的 url 得到原始 url 从而定位到正确的资源)



# 1. 数组中两个数的和为给定值

1\. Two Sum / 两数之和 (Easy)

[Leetcode](https://leetcode.com/problems/two-sum/description/) / [力扣](https://leetcode-cn.com/problems/two-sum/description/)

可以先对数组进行排序，然后使用双指针方法或者二分查找方法。这样做的时间复杂度为 O(NlogN)，空间复杂度为 O(1)。

用哈希表存储数组元素和索引的映射，在访问到 nums[i] 时，判断哈希表中是否存在 target - nums[i]，如果存在说明 target - nums[i] 所在的索引和 i 就是要找的两个数。该方法的时间复杂度为 O(N)，空间复杂度为 O(N)，使用空间来换取时间。

方法1：两遍哈希，先将所有数字及其下标存入哈希表，再逐个遍历数字，并在哈希表中查找对应的数

```cpp
// 两遍哈希
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> memo; // 键为数，值为此数在数组中的下标

        for(int i = 0; i < nums.size(); i++) {
            memo[nums[i]] = i; // 将各数添加到哈希表
        }

        for(int i = 0; i < nums.size(); i++) {
            if(memo.count(target - nums[i]) && memo[target - nums[i]] != i) // 存在，并且不是当前值
                return {i, memo[target - nums[i]]};
        }
        return {};
    }
};
```

方法2：一遍哈希，存入哈希表的过程中就查找哈希表中是否有对应的数，注意返回值的顺序

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> memo; // 键为数，值为此数在数组中的下标

        for(int i = 0; i < nums.size(); i++) {
            if(memo.count(target - nums[i])) // 存在另一个数
                return {memo[target - nums[i]], i};
            memo[nums[i]] = i; // 将各数添加到哈希表
        }
        return {};
    }
};

```



# 2. 判断数组是否含有重复元素

217\. Contains Duplicate (Easy)

[Leetcode](https://leetcode.com/problems/contains-duplicate/description/) / [力扣](https://leetcode-cn.com/problems/contains-duplicate/description/)

**题解**：

方法1：

将元素全部存入哈希表，若最终哈希表中元素个数小于原数组中元素个数，则数组中存在重复元素。

```cpp
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        unordered_map<int, int> hash;
        for(auto num : nums) hash[num]++;
        return hash.size() < nums.size();
    }
};
```



方法2：

存入哈希表的过程中就查找哈希表中是否存在当前元素。

```c
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        unordered_map<int, int> hash;
        for(auto num : nums) {
            if (hash.find(num) != hash.end()) return true;
            ++hash[num];
        }
        return false;
    }
};
```



# 3. 最长和谐序列

594\. Longest Harmonious Subsequence (Easy)

[Leetcode](https://leetcode.com/problems/longest-harmonious-subsequence/description/) / [力扣](https://leetcode-cn.com/problems/longest-harmonious-subsequence/description/)

和谐数组是指一个数组里元素的最大值和最小值之间的差别正好是1。现在，给定一个整数数组，你需要在所有可能的子序列中找到最长的和谐子序列的长度。

```html
Input: [1,3,2,2,5,2,3,7]
Output: 5
Explanation: The longest harmonious subsequence is [3,2,2,2,3].
```

**题解**：

和谐序列中最大数和最小数之差正好为 1，应该注意的是序列的元素不一定是数组的连续元素。我们只要找出来**相差为1的两个数的总共出现个数**就是一个和谐子序列的长度了。

方法1：先遍历一遍数组，使用哈希表建立每个数字跟其出现次数之间的映射，然后再遍历每个数字的时候，只需在 HashMap 中查找该数字加1是否存在，存在就更新结果 res。

```cpp
class Solution {
public:
    int findLHS(vector<int>& nums) {
        if(nums.empty()) return 0;
        unordered_map<int, int> m;
        int res = 0;
        for(auto num : nums) m[num]++; // 统计各元素出现次数
        for(auto i : m) { // 遍历哈希表
            if(m.count(i.first + 1)) // 若哈希表中存在当前元素值+1的元素
                res = max(res, m[i.first] + m[i.first + 1]); // 更新长度为当前长度和两者次数和的最大值
        }
        return res;
    }
};
```



方法2：也可以在一个 for 循环中搞定，遍历每个数字时，先累加其映射值，然后查找该数字加1是否存在，存在的话用 m[num] 和 m[num+1] 的和来更新结果 res，同时，还要查找该数字减1是否存在，存在的话用 m[num] 和 m[num-1] 的和来更新结果 res。

```cpp
class Solution {
public:
    int findLHS(vector<int>& nums) {
        int res = 0;
        unordered_map<int, int> m;
        for (int num : nums) { // 遍历各元素
            ++m[num]; // 存入哈希表
            if (m.count(num + 1)) // 比当前元素大1的元素存在于哈希表中，更新长度为两者个数和
                res = max(res, m[num] + m[num + 1]);
            if (m.count(num - 1)) // 比当前元素小1的元素存在于哈希表中，更新长度为两者个数和
                res = max(res, m[num] + m[num - 1]);
        }
        return res;
    }
};
```



# 4. 最长连续序列✏️

128\. Longest Consecutive Sequence (Hard)

[Leetcode](https://leetcode.com/problems/longest-consecutive-sequence/description/) / [力扣](https://leetcode-cn.com/problems/longest-consecutive-sequence/description/)

```html
Given [100, 4, 200, 1, 3, 2],
The longest consecutive elements sequence is [1, 2, 3, 4]. Return its length: 4.
```

要求以 O(N) 的时间复杂度求解。

```java
public int longestConsecutive(int[] nums) {
    Map<Integer, Integer> countForNum = new HashMap<>();
    for (int num : nums) {
        countForNum.put(num, 1);
    }
    for (int num : nums) {
        forward(countForNum, num);
    }
    return maxCount(countForNum);
}

private int forward(Map<Integer, Integer> countForNum, int num) {
    if (!countForNum.containsKey(num)) {
        return 0;
    }
    int cnt = countForNum.get(num);
    if (cnt > 1) {
        return cnt;
    }
    cnt = forward(countForNum, num + 1) + 1;
    countForNum.put(num, cnt);
    return cnt;
}

private int maxCount(Map<Integer, Integer> countForNum) {
    int max = 0;
    for (int num : countForNum.keySet()) {
        max = Math.max(max, countForNum.get(num));
    }
    return max;
}
```


