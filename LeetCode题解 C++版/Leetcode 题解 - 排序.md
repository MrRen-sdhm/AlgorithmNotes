# 排序✏️🥇⭐️❌

# 快速选择和堆

**1、快速选择**

用于求解   **TopK**   问题，也就是第 K 个元素的问题。

可以使用快速排序的 partition() 进行实现。需要先打乱数组，否则最坏情况下时间复杂度为 O(N<sup>2</sup>)。



**2、堆**

用于求解 **TopK** 问题，也就是 K 个最小元素的问题。可以维护一个大小为 K 的最小堆，最小堆中的元素就是最小元素。最小堆需要使用大顶堆来实现，大顶堆表示堆顶元素是堆中最大元素。这是因为我们要得到 k 个最小的元素，因此当遍历到一个新的元素时，需要知道这个新元素是否比堆中最大的元素更小，更小的话就把堆中最大元素去除，并将新元素添加到堆中。所以我们需要很容易得到最大元素并移除最大元素，大顶堆就能很好满足这个要求。

堆也可以用于求解 Kth Element 问题，得到了大小为 k 的最小堆之后，因为使用了大顶堆来实现，因此堆顶元素就是第 k 大的元素。

快速选择也可以求解 TopK Elements 问题，因为找到 Kth Element 之后，再遍历一次数组，所有小于等于 Kth Element 的元素都是 TopK Elements。

可以看到，快速选择和堆排序都可以求解 Kth Element 和 TopK Elements 问题。

------



## 1. 数组的第K大数

215\. Kth Largest Element in an Array / 数组中的第K个最大元素 (Medium)

[Leetcode](https://leetcode.com/problems/kth-largest-element-in-an-array/description/) / [力扣](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/description/)

```text
Input: [3,2,1,5,6,4] and k = 2
Output: 5
```

题目描述：找到从小到大排序后倒数第 k 个元素。

**题解**：

方法1：**堆**，时间复杂度 O(NlogK)，空间复杂度 O(K)。

```cpp
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        priority_queue<int, vector<int>, greater<int>> heap; // 小顶堆
        for(auto num : nums) {
            heap.push(num);
            if(heap.size() > k) heap.pop(); // 维护堆的大小为k
        }
        return heap.top();
    }
};
```

方法2：**快速选择**，时间复杂度 O(N)，空间复杂度 O(1)

此题要找到第k大数，即从小到大排序后倒数第 k 个数，可转换为求正数第`len - k + 1`个数

使用partition将数组划分为<=pivot和>=pivot两个部分，若pivot所处位置之前有k个数则舍弃后面的数，若pivot所处位置之前不到k个数则舍弃前面的数。下面的实现中while循环后，j 即为pivot所在位置，注意此 j 是相对整个数组而不是相对某个区间，因而包括pivot在内的左侧部分元素为[0, j]，长度为 j+1，若k<=j+1则第k个数在[l, j]区间内，否则在[j + 1, r]区间内

经过快速选择后，原数组中第k个数即为所求，其下标为[k - 1]

```cpp
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        k = nums.size() - k + 1; // 第k大的，转换为第k小的
        quick_select(nums, 0, nums.size() - 1, k);
        return nums[k - 1]; // 第k小的元素在原数组中的小标为k-1
    }

    void quick_select(vector<int>& q,int l,int r,int k)
    {
        if(l >= r) return;
        int x = q[l + r >> 1], i = l - 1, j = r + 1;
        while(i < j) {
            while(q[++i] < x);
            while(q[--j] > x);
            if(i < j) swap(q[i], q[j]);
        }
        // 0~j有j+1个数，因而k与j+1进行比较
        if(k <= j + 1) quick_select(q, l, j, k); // 第k个数在左侧
        else quick_select(q, j + 1, r, k); // 第k个数在右侧
    }
};
```



## 2. 有序矩阵的第K小元素

[Leetcode 378. 有序矩阵中第K小的元素 (Medium)](https://leetcode-cn.com/problems/kth-smallest-element-in-a-sorted-matrix/)

**题解**：

方法1：使用大顶堆

```cpp
class Solution {
public:
    int kthSmallest(vector<vector<int>>& matrix, int k) {
        priority_queue<int> heap;
        for(int i = 0; i < matrix.size(); i++) {
            for(int j = 0; j < matrix[0].size(); j++) {
                heap.push(matrix[i][j]);
                if(heap.size() > k) heap.pop();
            }
        }
        return heap.top();
    }
};
```



[方法2](https://www.cnblogs.com/grandyang/p/5727892.html)：二分查找✏️

1.找出二维矩阵中最小的数left，最大的数right，那么第k小的数必定在left~right之间
2.mid=(left+right) / 2；在二维矩阵中寻找小于等于mid的元素个数count
3.若这个count小于k，表明第k小的数在右半部分且不包含mid，即left=mid+1, right=right，又保证了第k小的数在left~right之间
4.若这个count大于k，表明第k小的数在左半部分且可能包含mid，即left=left, right=mid，又保证了第k小的数在left~right之间
5.因为每次循环中都保证了第k小的数在left~right之间，当left==right时，第k小的数即被找出，等于right

注意：这里的left mid right是数值，不是索引位置。

```cpp
class Solution {
public:
    int kthSmallest(vector<vector<int>>& matrix, int k) {
        int l = matrix[0][0], r = matrix.back().back();
        while (l < r) { // 每次循环保证第k小的数在start~end之间，当start==end，第k小的数就是start
            int mid = l + (r - l) / 2;
            int cnt = search_less_equal(matrix, mid); // 找二维矩阵中<=mid的元素总个数
            if (cnt >= k) r = mid; // 第k小元素在左半部分，可能包含mid
            else l = mid + 1; // 第k小元素在右半部分，不包含mid
        }
        return l;
    }

    int search_less_equal(vector<vector<int>>& matrix, int target) {
        int n = matrix.size(), i = n - 1, j = 0, res = 0;
        // 以列为单位找，找到最后一个<=mid的数
        while (i >= 0 && j < n) {
            if (matrix[i][j] <= target) {
                res += i + 1; // 第j列有i+1个元素<=mid
                ++j;
            } else {
                --i;
            }
        }
        return res;
    }
};
```



# 桶排序

## 1. 出现频率最高的K个元素✏️

347\. Top K Frequent Elements (Medium)

[Leetcode](https://leetcode.com/problems/top-k-frequent-elements/description/) / [力扣](https://leetcode-cn.com/problems/top-k-frequent-elements/description/)

```html
Given [1,1,1,2,2,3] and k = 2, return [1,2].
```

方法1：桶排序，时间复杂度O(n)，空间复杂度O(n)✏️

设置若干个桶，每个桶存储出现频率相同的数。桶的下标表示数出现的频率，即第 i 个桶中存储的数出现的频率为 i。

把数都放到桶之后，从后向前遍历桶，最先得到的 k 个数就是出现频率最多的的 k 个数。

```cpp
public List<Integer> topKFrequent(int[] nums, int k) {
    Map<Integer, Integer> frequencyForNum = new HashMap<>();
    for (int num : nums) {
        frequencyForNum.put(num, frequencyForNum.getOrDefault(num, 0) + 1);
    }
    List<Integer>[] buckets = new ArrayList[nums.length + 1];
    for (int key : frequencyForNum.keySet()) {
        int frequency = frequencyForNum.get(key);
        if (buckets[frequency] == null) {
            buckets[frequency] = new ArrayList<>();
        }
        buckets[frequency].add(key);
    }
    List<Integer> topK = new ArrayList<>();
    for (int i = buckets.length - 1; i >= 0 && topK.size() < k; i--) {
        if (buckets[i] == null) {
            continue;
        }
        if (buckets[i].size() <= (k - topK.size())) {
            topK.addAll(buckets[i]);
        } else {
            topK.addAll(buckets[i].subList(0, k - topK.size()));
        }
    }
    return topK;
}
```



方法2：哈希表+堆，时间复杂度 O(nlogk)，空间复杂度O(n) 

- 借助 哈希表 来建立数字和其出现次数的映射，遍历一遍数组统计元素的频率

- 维护一个元素数目为 k 的最小堆

- 每次都将新的元素与堆顶元素（堆中频率最小的元素）进行比较

- 如果新的元素的频率比堆顶端的元素大，则弹出堆顶端的元素，将新的元素添加进堆中

- 最终，堆中的 k 个元素即为前 k 个高频元素

首先，遍历一遍数组统计元素的频率，这一系列操作的时间复杂度是 O(n)；接着，遍历用于存储元素频率的 map，如果元素的频率大于最小堆中顶部的元素，则将顶部的元素删除并将该元素加入堆中，这里维护堆的数目是 k，所以这一系列操作的时间复杂度是 O(nlogk) 的；因此，总的时间复杂度是 O(nlog⁡k)。

最坏情况下（每个元素都不同），map 需要存储 n 个键值对，优先队列需要存储 k 个元素，因此，空间复杂度是 O(n)。

```cpp
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> ump;
        priority_queue<pair<int, int>> pq; // 大顶堆

        for(int num : nums) ump[num]++; // 建立数字及出现次数的映射,键为数字值为频率
        for(auto it : ump) pq.push({it.second, it.first}); // 按 频率-数字 的顺序放入堆中，按频率排序

        vector<int> res;
        for(int i = 0; i < k; i++) { // 取出堆顶的前k个数即为出现频率前k高的数
            res.push_back(pq.top().second); pq.pop(); // 取出时first为频率，second为数字
        }
        return res;
    }
};
```



## 2. 按照字符出现次数对字符串排序

451\. Sort Characters By Frequency (Medium)

[Leetcode](https://leetcode.com/problems/sort-characters-by-frequency/description/) / [力扣](https://leetcode-cn.com/problems/sort-characters-by-frequency/description/)

```html
Input:
"tree"

Output:
"eert"

Explanation:
'e' appears twice while 'r' and 't' both appear once.
So 'e' must appear before both 'r' and 't'. Therefore "eetr" is also a valid answer.
```

```cpp
public String frequencySort(String s) {
    Map<Character, Integer> frequencyForNum = new HashMap<>();
    for (char c : s.toCharArray())
        frequencyForNum.put(c, frequencyForNum.getOrDefault(c, 0) + 1);

    List<Character>[] frequencyBucket = new ArrayList[s.length() + 1];
    for (char c : frequencyForNum.keySet()) {
        int f = frequencyForNum.get(c);
        if (frequencyBucket[f] == null) {
            frequencyBucket[f] = new ArrayList<>();
        }
        frequencyBucket[f].add(c);
    }
    StringBuilder str = new StringBuilder();
    for (int i = frequencyBucket.length - 1; i >= 0; i--) {
        if (frequencyBucket[i] == null) {
            continue;
        }
        for (char c : frequencyBucket[i]) {
            for (int j = 0; j < i; j++) {
                str.append(c);
            }
        }
    }
    return str.toString();
}
```



# 荷兰国旗问题

荷兰国旗包含三种颜色：红、白、蓝。

有三种颜色的球，算法的目标是将这三种球按颜色顺序正确地排列。它其实是三向切分快速排序的一种变种，在三向切分快速排序中，每次切分都将数组分成三个区间：小于切分元素、等于切分元素、大于切分元素，而该算法是将数组分成三个区间：等于红色、等于白色、等于蓝色。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/7a3215ec-6fb7-4935-8b0d-cb408208f7cb.png"/> </div><br>


## 1. 按颜色进行排序

75\. Sort Colors (Medium)

[Leetcode](https://leetcode.com/problems/sort-colors/description/) / [力扣](https://leetcode-cn.com/problems/sort-colors/description/)

```html
Input: [2,0,2,1,1,0]
Output: [0,0,1,1,2,2]
```

题目描述：只有 0/1/2 三种颜色。

**题解**：使用双指针，分别指向红色区域的末尾和蓝色区域的开头。

- 当前指针指向0则红色区域末尾指针右移，将0与红色区域末尾指针指向的数字交换，即红色区域扩大一位，然后当前指针向后移；
- 当前指针指向2则蓝色区域开头指针左移，将2与蓝色区域开头指针指向的元素交换，但蓝色指针指向的数字大小不确定，因而需要再次判断大小，当前指针保持不动；
- 若当前指针指向1则不作交换，直接指向下一个元素。
- 循环结束的条件就是当前指针必须在蓝色区域开头指针的左侧。



写法1：while循环，需要使用一个指针指向当前位置。**从右侧交换过来一个数，则还需判断交换过来这个数的大小，因而交换后curr指针不动**。

```cpp
class Solution {
public:
    void sortColors(vector<int>& nums) {
        int l = -1, r = nums.size(), curr = 0;
        while(curr < r) {
            if(nums[curr] < 1) swap(nums[++l], nums[curr++]);
            else if(nums[curr] > 1) swap(nums[--r], nums[curr]);
            else curr++;
        }
    }
};
```



写法2：for循环逐个枚举各个位置，若从右侧交换过来一个数。则还需判断交换过来这个数的大小，因而交换后需要 i--，然后当前循环结束 i 会加1，从而 i 保持不变。

```cpp
class Solution {
public:
    void sortColors(vector<int>& nums) {
        int l = -1, r = nums.size();
        for(int i = 0; i < r; i++) {
            if(nums[i] < 1) swap(nums[++l], nums[i]);
            else if(nums[i] > 1) swap(nums[--r], nums[i--]);
        }
    }
};
```

