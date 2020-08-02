# 剑指offer题解 C++版 ⚔️ ✏️🥇⭐️❌

**参考资料:** [**剑指offer题解-CyC2018**](https://cyc2018.github.io/CS-Notes/#/notes/%E5%89%91%E6%8C%87%20Offer%20%E9%A2%98%E8%A7%A3%20-%20%E7%9B%AE%E5%BD%951)

⭐️表示重点题目  ❌表示繁杂的题 🥇表示推荐的解法



## 数组

### 面试题3.1 找出数组中重复的数字⭐️

【[OJ](https://www.nowcoder.com/practice/623a5ac0ea5b4e5f95552655361ae0a8?tpId=13&tqId=11203&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWIng](https://www.acwing.com/problem/content/14/)】在一个**长度为n**的数组里的所有数字都在0到n-1的范围内。 数组中某些数字是重复的，但不知道有几个数字是重复的。也不知道每个数字重复几次。请找出数组中任意一个重复的数字。 例如，如果输入长度为7的数组{2,3,1,0,2,5,3}，那么对应的输出是第一个重复的数字2。

```
给定 nums = [2, 3, 5, 4, 3, 2, 6, 7]。
返回 2 或 3。
```

**题解**：要求时间复杂度 O(N)，空间复杂度 O(1)。因此不能使用排序的方法，也不能使用额外的标记数组。

对于这种数组元素在 [0, n-1] 范围内的问题，可以**将值为 i 的元素调整到第 i 个位置上**进行求解。本题要求找出重复的数字，因此在调整过程中，如果第 i 位置上已经有一个值为 i 的元素，就可以知道 i 值重复。

**关键点：只要当前位置的数nums[i]与下标不对应，就将nums[i]与其对应下标位置的数交换，若对应下标位置的数与nums[i]相等则为重复数。**

以 (2, 3, 1, 0, 2, 5) 为例，遍历到位置 4 时，该位置上的数为 2，但是第 2 个位置上已经有一个 2 的值了，因此可以知道 2 重复：

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/643b6f18-f933-4ac5-aa7a-e304dbd7fe49.gif" width="350px" /> </div>

```cpp
class Solution {
public:
    bool duplicate(int numbers[], int length, int* duplication) {
        if(numbers == nullptr || length <= 0) // 处理错误输入
            return false;
        
        for(int i = 0; i < length; ++i) { // 检查数组是否合理
            if(numbers[i] < 0 || numbers[i] > length - 1)
                return false;
        }
        
        for(int i = 0; i < length; ++i) { // 遍历各元素
            while(numbers[i] != i) { // 将numbers[i]放到numbers[numbers[i]]位置，直到numbers[i] == i
                if(numbers[i] == numbers[numbers[i]]) { // numbers[i]位置存在与i位置相等的值，即重复值
                    *duplication = numbers[i];
                    return true;
                }
                // 交换numbers[i] 和 numbers[numbers[i]]
                swap(numbers[i], numbers[numbers[i]]);
            }
        }
        
        return false;
    }
};
```



### 面试题3.2 不修改数组找出重复的数字（核心思想：二分查找）⭐️

【[OJ](https://www.acwing.com/problem/content/15/) / [AcWing](https://www.acwing.com/problem/content/15/) / [Leetcode](https://leetcode-cn.com/problems/find-the-duplicate-number/)】给定一个长度为 n+1 的数组`nums`，数组中所有的数均在 1∼n 的范围内，其中 n≥1。

请找出数组中任意一个重复的数，但不能修改输入的数组，要求空间复杂度为O(1)。

```
给定 nums = [2, 3, 5, 4, 3, 2, 6, 7]。
返回 2 或 3。
```

**题解**：使用二分查找，时间复杂度O(nlogn)

此题不允许使用额外空间，也不允许修改原数组，因而无法排序。但是题中限定数据范围为`[1, n]`，而序列`1,2...,n`是有序的，因而可以**在`[1, n]`中进行二分查找，注意不是在nums数组中进行查找**。`mid = (1 + n) / 2`，接下来判断最终答案是在 `[1, mid]` 中还是在 `[mid + 1, n]` 中。

为了缩小区间，需要统计原数组中小于等于 `mid` 的元素个数，记为 `count`。如果 `count > mid` ，根据鸽巢原理，在 `[1,mid]` 范围内的数字个数超过了 `mid` ，所以区间中`[1, mid]`一定有一个重复数字，保留区间`[1, mid]`。否则重复元素在`[mid + 1, n]`中，切除区间`[mid + 1, n]`。

最终两个指针的值即为重复数字！

```cpp
class Solution {
public:
    int duplicateInArray(vector<int>& nums) {
        if(nums.empty()) return -1;
        int n = nums.size();
        
        int l = 1, r = n;
        while(l < r) {
            int mid = l + r >> 1;
            int cnt = 0;
            for(int i = 0; i < n; i++) // 统计原数组中<=mid的元素个数
                if(nums[i] <= mid) cnt++;
            if(cnt > mid) r = mid; // 重复数字在区间[1, mid]中
            else l = mid + 1; // 重复数字在区间[mid + 1, n]中
        }
        return l;
    }
};
```



### 面试题4 二维数组中的查找

【[OJ](https://www.nowcoder.com/practice/abc3fe2ce8e146608e868a70efebf62e?tpId=13&tqId=11154&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/16/)】在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

```html
Consider the following matrix:
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]

Given target = 5, return true.
Given target = 20, return false.
```

**题解**：要求时间复杂度 O(M + N)，空间复杂度 O(1)。其中 M 为行数，N 为 列数。该二维数组中的一个数，小于它的数一定在其左边，大于它的数一定在其下边。因此，从右上角或左下角开始查找，就可以根据 target 和当前元素的大小关系来缩小查找区间，当前元素的查找区间为左下角或右上角的所有元素。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/35a8c711-0dc0-4613-95f3-be96c6c6e104.gif" width="370px" /> </div>

```cpp
class Solution {
public:
    bool Find(int target, vector<vector<int> > array) {
        int rows = array.size();
        if (rows == 0) return false;
        int cols = array[0].size();
        if (cols == 0) return false;
        
        int row = rows - 1;
        int col = 0;
        while(row >= 0 && col < cols) {
            if (array[row][col] < target) col++;
            else if (array[row][col] > target) row--;
            else return true;
        }
        
        return false;
    }
};
```



### 面试题11 旋转数组的最小数字（核心思想：二分查找）⭐️

【[OJ](https://www.nowcoder.com/practice/9f3231a991af4f55b95579b44b7a01ba?tpId=13&tqId=11159&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/20/)】把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个**非递减排序**的数组的一个旋转，输出旋转数组的最小元素。例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。数组中**可能存在重复数字**。

```
输入：nums=[2,2,2,0,1]
输出：0
```

**题解**：

旋转之后的数组实际上可以划分为两个排序的子数组，而且前面子数组的元素都大于或者等于后面子数组的元素。注意到最小的元素刚好是这两个子数组的分界线。在排序的数组中我们可以用**二分查找**法实现**O(logn)**的查找。

但是需要考虑一种情况就是，**数组在重复元素区段翻转**，例如[1,2,3,3,4,5]翻转后成为[3,4,5,1,2,3]此时无法使用二分查找，需要将后半段中的重复数字去除后再使用二分查找。

若去重后的数组尾元素大于等于数组尾元素，说明数组单调递增或全部元素相等，直接返回首元素即可，不可再进行二分查找。

```cpp
class Solution {
public:
    int minNumberInRotateArray(vector<int> nums) {
        int n = nums.size() - 1;
        if(nums.empty()) return -1;
        
        while(n > 0 && nums[n] == nums[0]) n--; // 去除后半部分的重复数字
        if(nums[n] >= nums[0]) return nums[0]; // 去重后的数组单调递增或元素相同，返回首元素
        int l = 0, r = n;
        while(l < r) {
            int mid = l + r >> 1;
            if(nums[mid] < nums[0]) r = mid; // 最小元素在左半边
            else l = mid + 1;
        }
        return nums[l];
    }
};
```



### 面试题21 调整数组顺序使奇数位于偶数前面（核心思想：双指针）⭐️

【[OJ](https://www.nowcoder.com/practice/beb5aa231adc45b2a5dcc5b62c93f593?tpId=13&tqId=11166&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/30/)】输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分，并保证奇数和奇数，偶数和偶数之间的相对位置不变。

**题解**：

1、若相对位置可变，使用双指针分别从首尾查找偶数和奇数，并交换。时间复杂度约O(n/2)，空间复杂度O(1)

用两个指针分别从首尾开始，往中间扫描。扫描时保证第一个指针前面的数都是奇数，第二个指针后面的数都是偶数。

每次迭代时需要进行的操作：

第一个指针一直往后走，直到遇到第一个偶数为止；
第二个指针一直往前走，直到遇到第一个奇数为止；
交换两个指针指向的位置上的数，再进入下一层迭代，直到两个指针相遇为止；

```cpp
class Solution {
public:
    // 使用双指针，不稳定
    void reOrderArray(vector<int> &array) { // 类似快排的partition, 不稳定
        if(array.empty()) return;
        
        int l = 0, r = array.size() - 1;
        while(l < r) {
            while(l < r && array[l] % 2 == 1) l++; // 左指针向后移动，直到其指向偶数
            while(l < r && array[r] % 2 == 0) r--; // 右指针向前移动，直到其指向奇数
            if(l < r) swap(array[l], array[r]); // 交换奇数和偶数
        }
    }
};
```



2、若相对位置不变，需使用辅助数组并遍历两次，先放入奇数再放入偶数。时间复杂度约O(2*n)，空间复杂度O(n)

```cpp
class Solution {
public:
    void reOrderArray(vector<int> &array) { // 准备辅助数组，依次放入奇数和偶数
        if(array.empty()) return;
        
        vector<int> res;
        for(int i = 0; i < array.size(); i++) {
            if((array[i] & 0x1) != 0) // 奇数
                res.push_back(array[i]);
        }
        for(int i = 0; i < array.size(); i++) {
            if((array[i] & 0x1) == 0) // 偶数
                res.push_back(array[i]);
        }
        array = res;
    }
};
```



### 面试题29 顺时针打印矩阵

【[OJ](https://www.nowcoder.com/practice/9b4c81a02cd34f76be2659fa0d54342a?tpId=13&tqId=11172&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下4 X 4矩阵： 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.

```cpp
class Solution {
public:
    static vector<int> printMatrix(vector<vector<int> > matrix) {
        vector<int> res;

        if(matrix.empty()) return res;

        int tR = 0, tC = 0, dR = matrix.size() - 1, dC = matrix[0].size()- 1;

        while(tR <= dR && tC <= dC) {
            if(tR == dR) { // 打印一行
                for(int i = tC; i <= dC; ++i) {
                    res.push_back(matrix[tR][i]);
                }
            } else if(tC == dC) { // 打印一列
                for(int i = tR; i <= dR; ++i) {
                    res.push_back(matrix[i][tC]);
                }
            } else { // 打印四个边
                int currR = tR;
                int currC = tC;
                while(currC != dC)
                    res.push_back(matrix[tR][currC++]);

                while(currR != dR)
                    res.push_back(matrix[currR++][dC]);

                while(currC != tC)
                    res.push_back(matrix[dR][currC--]);

                while(currR != tR)
                    res.push_back(matrix[currR--][tC]);
            }

            tR++, tC++, dR--, dC--; // 缩小边长
        }

        return res;
    }
};
```



### 面试题39 数组中出现次数超过—半的数字

【[OJ](https://www.nowcoder.com/practice/e8a1b01a2df14cb2b228b30ee6a92163?tpId=13&tqId=11181&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/48/)】数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。

**题解**：使用 cnt 来统计一个元素出现的次数，当遍历到的元素和统计元素相等时，令 cnt++，否则令 cnt--。如果前面查找了 i 个元素，且 cnt == 0，说明前 i 个元素没有 majority，或者有 majority，但是出现的次数少于 i / 2 ，因为如果多于 i / 2 的话 cnt 就一定不会为 0 。此时剩下的 n - i 个元素中，majority 的数目依然多于 (n - i) / 2，因此继续查找就能找出 majority。

**若某数出现次数超过一半，那么两两比较，至少有一次出现相邻两数相等，即使间隔排列，如12322**

```cpp
class Solution {
public:
    int MoreThanHalfNum_Solution(vector<int>& nums) {
        int major = nums[0]; int cnt = 1;
        for(int i = 1; i < nums.size(); i++) {
            if(nums[i] == major) cnt++; // 相等则数量加1
            else cnt--; // 否则减1
            
            if(cnt == 0) { // 数量减到0，更换数字
                major = nums[i];
                cnt = 1;
            }
        }
        cnt = 0;
        for(auto num : nums) // 统计最终的数字在数组中出现的次数
            if(num == major) cnt++;
        return cnt > nums.size() / 2 ? major : 0; // 确实超过一半，返回此数，否则返回0
    }
};
```



### 面试题40 最小的 K 个数（核心思想：partition）⭐️⭐️

【[OJ](https://www.nowcoder.com/practice/6a296eb82cf844ca8539b57c23e6e9bf?tpId=13&tqId=11182&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/description/49/)】输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4,。

**题解**：

🥇方法1：快速选择，时间复杂度O(n)，空间复杂度O(1)

使用partition将数组划分为<=pivot和>=pivot两个部分，若pivot所处位置之前有k个数则舍弃后面的数，若pivot所处位置之前不到k个数则舍弃前面的数。模板中while循环后，j 即为pivot所在位置，注意此 j 是**相对整个数组**而不是相对某个区间，因而包括pivot在内的左侧部分元素为[0, j]，长度为 j+1，若k<=j+1则第k个数在[l, j]区间内，否则在[j + 1, r]区间内

经过快速选择后，原数组中第k个数即为所求，其下标为[k - 1]

**注意**：每次快速选择仅能找出第 k 个数，而不能一次性获得前 k 个数

```cpp
vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
        vector<int> res;
        if(input.size() == 0 || k > int(input.size()) || k <= 0)
            return res;
        
        for(int i = 1; i <= k; i++) { // 每次快速选择仅能获取第k个数
            quick_select(input, 0, input.size() - 1, i);
            res.push_back(input[i - 1]); // 第k小的元素在原数组中的小标为k-1
        }
        return res;
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



🥇方法2：使用堆(priority_queue)，时间复杂度为O(nlogk)，空间复杂度为O(k)，特别适合处理海量数据

应该使用大顶堆来维护最小堆，而不能直接创建一个小顶堆并设置一个大小，企图让小顶堆中的元素都是最小元素。

维护一个大小为 K 的最小堆过程如下：在添加一个元素之后，如果大顶堆的大小大于 K，那么需要将大顶堆的堆顶元素去除。

**可以一次性获得前 k 个数**

C++中 priority_queue 默认即为最大堆

定义最大堆：`priority_queue<int> maxHeap;`

定义最小堆：`priority_queue<int, vector<int>, greater<int>> maxHeap;`

```cpp
class Solution {
public:
    vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
        vector<int> res;
        if(input.size() == 0 || k > int(input.size()) || k <= 0) return res;
        
        priority_queue<int> heap; // 最大堆，最大的元素在队列前面
        for (int num : input) {
            heap.push(num);
            if (heap.size() > k) heap.pop();
        }
        while(!heap.empty()) {
            res.push_back(heap.top());
            heap.pop();
        }
        return res;
    }
};
```



方法3：使用红黑树(multiset)，时间复杂度为O(nlogk)，空间复杂度为O(k)，特别适合处理海量数据

```cpp
class Solution {
public:
    // 使用红黑树来保存最小的k个数
    vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
        if(input.size() == 0 || k > int(input.size()) || k <= 0)
            return vector<int>();
        
        //仿函数中的greater<T>模板，从大到小排序
        multiset<int, greater<int> > leastNums;
        vector<int>::iterator vec_it = input.begin();
        for(; vec_it!=input.end(); ++vec_it) {
            //将前k个元素插入集合
            if(leastNums.size() < k)
                leastNums.insert(*vec_it);
            else {
                //第一个元素是最大值
                multiset<int, greater<int> >::iterator greatest_it = leastNums.begin();
                //如果后续元素<第一个元素，删除第一个，加入当前元素
                if(*vec_it < *(leastNums.begin())) {
                    leastNums.erase(greatest_it);
                    leastNums.insert(*vec_it);
                }
            }
        }
        
        return vector<int>(leastNums.begin(), leastNums.end());
    }
};
```



### 面试题42 连续子数组的最大和

【[OJ](https://www.nowcoder.com/practice/459bd355da1549fa8a49e350bf3df484?tpId=13&tqId=11183&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/50/)】输入一个 **非空** 整型数组，数组里的数可能为正，也可能为负。数组中一个或连续的多个整数组成一个子数组。求所有子数组的和的最大值。要求时间复杂度O(n)

```
输入：[1, -2, 3, 10, -4, 7, 2, -5]
输出：18
```

**题解**：从左至右求和，若和小于0，说明前面序列的和不可能为最大值，置当前值为新的和。最终取连续序列中和最大的那一个。也可通过动态规划的思想分析此问题。

```cpp
class Solution {
public:
    int FindGreatestSumOfSubArray(vector<int> array) {
        if(array.empty()) return 0;
        int res = 0x80000000, sum = 0; // 最小的负数
        for(int x : array) {
            if(sum < 0) sum = x; // sum小于0则置当前值为sum
            else sum += x; // 否则sum加上当前值
            res = max(res, sum); // 保存最大的和
        }
        return res;
    }
};
```



### 面试题45 把数组排成最小的数（核心思想：利用字符串处理大数）

【[OJ](https://www.nowcoder.com/practice/8fecd3f8ba334add803bf2a06af1b993?tpId=13&tqId=11185&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/54/)】输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。

```
输入：[3, 32, 321]
输出：321323
```

**题解**：可以看成是一个排序问题，在比较两个字符串 S1 和 S2 的大小时，应该比较的是 S1+S2 和 S2+S1 的大小，如果 S1+S2 < S2+S1，那么应该把 S1 排在前面，否则应该把 S2 排在前面。

写法1：

```cpp
class Solution {
public:
    string PrintMinNumber(vector<int> numbers) {
        if(numbers.empty()) return "";
        
        sort(numbers.begin(), numbers.end(), cmp); // 根据转换为字符串后的和进行排序
        string res;
        for(int num : numbers)
            res += to_string(num);
        return res;
    }
    
    static bool cmp(int a, int b){ // 转换为字符串求和并比较，需为static函数
        string A = to_string(a) + to_string(b);
        string B = to_string(b) + to_string(a);
        return A < B;
    }
};
```



写法2：

```cpp
class Solution {
public:
    string printMinNumber(vector<int>& nums) {
        if(nums.empty()) return "";

        sort(nums.begin(), nums.end(), [](int& a, int& b) { // lambda表达式
            return to_string(a) + to_string(b) < to_string(b) + to_string(a);
        });
        string res;
        for(auto num : nums)
            res += to_string(num);
        return res;
    }
};
```



### 面试题51 数组中的逆序对（核心思想：归并排序）⭐️⭐️

【[OJ](https://www.nowcoder.com/practice/96bd6684e04a44eb80e6a68efc0ec6c5?tpId=13&tqId=11188&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组,求出这个数组中的逆序对的总数P。并将P对1000000007取模的结果输出。 即输出P%1000000007

**题解**：

方法1：暴力求解，时间复杂度O(n^2)，空间复杂度O(1)

```cpp
class Solution {
public:
    int InversePairs_(vector<int> data) {
        int res = 0;
        if(data.empty()) return res;
        
        for(int i = 0; i < data.size(); ++i) {
            for(int j = i + 1; j < data.size(); ++j) {
                if(data[i] > data[j])
                    res++;
            }
        }
        return res;
    }
};
```

方法2：归并排序的过程中查找逆序对，时间复杂度O(nlogn)，空间复杂度O(n)。合并的过程中，左侧区间和右侧区间中所有元素分别都是排序好的。若左侧某元素比右侧元素大，则此元素后面的所有元素都比右侧元素大。因为右侧的当前元素会移入辅助数组（右指针右移），因而需要一次性将左侧比此元素大的数字个数加上。

<div align="center"> <img src="https://gitee.com//MrRen-sdhm/Images/raw/master/img/20200530124250.gif" width="600px" /> </div>

```cpp
class Solution {
public:
    int res = 0;
    int InversePairs(vector<int> &data) {
        if(data.size() < 2)
            return 0 ;
        MergeSort(data, 0, data.size() - 1);
        return res;
    }
    
    void MergeSort(vector<int> &arr, int l, int r) {
        if(l >= r) return;
        
        int tmp[arr.size()];
        int mid = l + r >> 1;
        MergeSort(arr, l, mid);  MergeSort(arr, mid + 1, r);
        
        int k = 0, i = l, j = mid + 1;
        while(i <= mid && j <= r) {
            if(arr[i] <= arr[j]) tmp[k++] = arr[i++];
            else if(arr[i] > arr[j]) {
                tmp[k++] = arr[j++];
                res = (res + (mid - i + 1)) % 1000000007; // 计算逆序对数量
            }
        }
        while(i <= mid) tmp[k++] = arr[i++];
        while(j <= r) tmp[k++] = arr[j++];
        
        for(int i = l, j = 0; i <= r; i++, j++) arr[i] = tmp[j];
    }
};
```



### 面试题57.1 和为S的两个数字（核心思想：双指针/哈希表）⭐️

【[OJ](https://www.nowcoder.com/practice/390da4f7a00f44bea7c2f3d19491311b?tpId=13&tqId=11195&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/71/)】输入一个递增**排序**的数组和一个数字S，在数组中查找两个数，使得他们的和正好是S，如果有多对数字的和等于S，输出两个数的乘积最小的。

**题解**：可以利用数组已排序的特性，设两个指针，分别指向最小值和最大值，若和小于目标和则最小值指针右移，若和大于目标值最大值指针左移

方法1：双指针，时间复杂度O(n)，空间复杂度O(1)；若数组未排序，则复杂度为排序的时间复杂度O(nlogn)，空间复杂度O(logn)

```cpp
class Solution {
public:
    vector<int> FindNumbersWithSum(vector<int>& array, int sum) {
        if(array.empty()) return {};
        //sort(nums.begin(), nums.end());
        int l = 0, r = array.size() - 1; // 左指针指向较小的数， 右指针指向较大的数
        while(l < r) {
            long long cur_sum = array[l] + array[r];
            if(cur_sum < sum) l++; // 当前和小于目标和，取较大的数加入组合
            else if(cur_sum > sum) r--; // 当前和大于目标和，取较小的数加入组合
            else return {array[l], array[r]};
        }
        return {};
    }
};
```



方法2：哈希表，**适用于未排序的数组**。插入操作的时间复杂度为O(1)，因而总的时间复杂度O(n)，空间复杂度O(n)

```cpp
class Solution {
public:
    vector<int> FindNumbersWithSum(vector<int>& array, int sum) {
        if(array.empty()) return {};
        unordered_set<int> hash;
        for(int num : array) {
            if(hash.count(sum - num)) return {sum - num, num}; // 哈希表中有另一半
            hash.insert(num);
        }
        return {};
    }
};
```



### 面试题57.2  和为S的连续正数序列（核心思想：双指针）

【[OJ](https://www.nowcoder.com/practice/c451a3fd84b64cb19485dad758a55ebe?tpId=13&tqId=11194&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/72/)】输入一个正数s，打印出所有和为s的连续正数序列（至少含有两个数）。例如输入15，由于1+2+3+4+5=4+5+6=7+8= 15，所以结果打印出3个连续序列1～5、4～6和7～8。

```
输入：15
输出：[[1,2,3,4,5],[4,5,6],[7,8]]
```



**题解**：本质是在**已排序**的序列中查找和为某数的连续序列。设两个指针，起初指向1和2，两指针构成滑动窗口，若窗口中的和小于目标和，则窗口加宽（右指针右移），若窗口中的和大于目标和，则窗口缩窄（左指针右移）。注意**单调性**：两个指针仅向右移

```cpp
class Solution {
public:
    vector<vector<int> > FindContinuousSequence(int sum) {
        int l = 1, r = 2;
        vector<vector<int>> res;
        while(l < r) {
            long long cur_sum = (r + l) * (r - l + 1) / 2; // 求和公式(a0+an)*n/2
            if(cur_sum == sum) {
                vector<int> vec;
                for(int i = l; i <= r; i++) vec.push_back(i);
                res.push_back(vec);
                l++; // 找到和为sum的序列了，缩小区间
            }
            else if (cur_sum < sum) r++; // 和小于目标和，扩大区间
            else l++; // 和大于目标和，缩小区间
        }
        return res;
    }
};
```



### 面试题53.1 数字在排序数组中出现的次数（核心思想：二分查找）⭐️

【[OJ](https://www.nowcoder.com/practice/70610bf967994b22bb1c26f9ae901fa2?tpId=13&tqId=11190&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】统计一个数字在排序数组中出现的次数。

**题解**：利用二分查找，但查找某数第一次出现和最后一次出现的位置，而不是单纯的判断某个数在不在数组中

```cpp
class Solution {
public:
    int GetNumberOfK(vector<int> data ,int k) {
        if(data.empty()) return 0;

        int first = GetFirstK(data, k);
        int last = GetLastK(data, k);

        if(first > -1 && last > -1)
            return last - first + 1;

        return 0;
    }

    int GetFirstK(vector<int> data, int k) { // 二分查找排序数组中第一个与k相等的数
        int l = 0, h = data.size() - 1;
        while(l <= h) { // 注意等于也符合循环条件
            int mid = l + (h - l) / 2;
            if(data[mid] == k) {
                if((mid > 0 && data[mid - 1] != k) || mid == 0) {
                    return mid;
                }
                else {
                    h = mid - 1;
                }
            } else if(data[mid] > k) {
                h = mid - 1;
            } else {
                l = mid + 1;
            }
        }

        return -1;
    }

    int GetLastK(vector<int> data, int k) { // 二分查找排序数组中最后一个与k相等的数
        int l = 0, h = data.size() - 1;
        while(l <= h) { // 注意等于也符合循环条件
            int mid = l + (h - l) / 2;
            if(data[mid] == k) {
                if((mid < data.size() - 1 && data[mid + 1] != k) || mid == data.size() - 1) {
                    return mid;
                }
                else {
                    l = mid + 1;
                }
            } else if(data[mid] > k) {
                h = mid - 1;
            } else {
                l = mid + 1;
            }
        }

        return -1;
    }
    
    int GetNumberOfK_(vector<int> data ,int k) {
        if(data.empty()) return 0;
        
        int cnt = 0;
        bool startFlag = false;
        for(int i = 0; i < data.size(); ++i) {
            if(data[i] == k) {
                cnt ++;
                startFlag = true;
            } else if(data[i] != k && startFlag == true) {
                break;
            }
        }
        
        return cnt;
    }
};
```



### 面试题53.2 0到n-1中缺失的数字（核心思想：二分查找）

【[AcWing](https://www.acwing.com/problem/content/64/) / [Leetcode](https://leetcode-cn.com/problems/que-shi-de-shu-zi-lcof/)】一个长度为n-1的递增排序数组中的所有数字都是唯一的，并且每个数字都在范围0到n-1之内。在范围0到n-1的n个数字中有且只有一个数字不在该数组中，请找出这个数字。

```
输入：[0,1,2,4]
输出：3
```

**题解**：二分查找，时间复杂度O(logn)

这道题目给定的是递增数组，假设数组中第一个缺失的数是 x，那么数组中的数如下所示：

<img src="https://gitee.com//MrRen-sdhm/Images/raw/master/img/20200602131633.png" alt="缺失数字.png" style="zoom:67%;" />


可以看出，数组左边蓝色部分都满足`nums[i] == i`，数组右边橙色部分都不满足`nums[i] == i`，因此可以二分出分界点 x 的值。

```cpp
class Solution {
public:
    int getMissingNumber(vector<int>& nums) {
        int l = 0, r = nums.size();
        while(l < r) {
            int mid = l + r >> 1;
            int cnt = 0;
            if(nums[mid] != mid) r = mid;
            else l = mid + 1;
        }
        return l;
    }
};
```



### 面试题53.3 数组中数值和下标相等的元素

【[AcWing](https://www.acwing.com/problem/content/65/)】假设一个单调递增的数组里的每个元素都是整数并且是唯一的。请编程实现一个函数找出数组中任意一个数值等于其下标的元素。例如，在数组[-3, -1, 1, 3, 5]中，数字3和它的下标相等。如果不存在，则返回-1。

```
输入：[-3, -1, 1, 3, 5]
输出：3
```

**题解**：数值大于下标，说明数值等于下标的元素在左边

```cpp
class Solution {
public:
    int getNumberSameAsIndex(vector<int>& nums) {
        int l = 0, r = nums.size() - 1;
        while(l < r) {
            int mid = l + r >> 1;
            if(nums[mid] >= mid) r = mid;
            else l = mid + 1;
        }
        if(nums[l] != l) return -1;
        return l;
    }
};
```



### 面试题62 圆圈中最后剩下的数字

【[OJ](https://www.nowcoder.com/practice/f78a359491e64a50bce2d89cff857eb6?tpId=13&tqId=11199&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [力扣](https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/) / [AcWIng](https://www.acwing.com/problem/content/78/)】0,1,,n-1这n个数字排成一个圆圈，从数字0开始，每次从这个圆圈里删除第m个数字。求出这个圆圈里剩下的最后一个数字。

例如，0、1、2、3、4这5个数字组成一个圆圈，从数字0开始每次删除第3个数字，则删除的前4个数字依次是2、0、4、1，因此最后剩下的数字是3。

**题解**：可使用list/vector模拟，也可根据公式求解。

因为C++中list不支持迭代器+n操作，删除元素必须从头查找第n个元素，使用不便，推荐使用vector。

🥇方法1：使用vector模拟，推荐

```cpp
class Solution
{
public:
    int LastRemaining_Solution(int n, int m)
    {
        if(n < 1||m < 1) return -1;
         
        vector<int> nums(n);
        for(int i = 0; i < n; i++)
            nums[i] = i;
        
        int i = 0;
        while(nums.size() > 0) {
            i = (i + m - 1) % nums.size(); // 出队位置索引
            nums.erase(nums.begin() + i); // 删除出队元素
        }
        return nums[i];
    }
};
```

方法2：找出规律, 通项为：f(n,m) = {f(n-1,m) + m} % n。

```cpp
class Solution
{
public:
    int LastRemaining_Solution(int n, int m) {
        if(n < 1||m < 1) return -1;
         
        int last = 0;
        for(int i = 2; i <= n; i++){
            last = (last + m) % i;
        }
        return last;
    }
};

// 递归写法
class Solution {
public:
    int LastRemaining_Solution(int n, int m) {
        if (n == 1) return 0;
        return (LastRemaining_Solution(n - 1, m) + m) % n;
    }
};
```

方法3：C++实现 list容器+其迭代器实现圆形链表 （约瑟夫环问题）

```cpp
class Solution {
public:
    int LastRemaining_Solution(int n, int m)
    {
        if(n < 1|| m < 1)
            return -1;
            
        list<int> numbers;
        for(int i = 0; i < n; i++)
            numbers.push_back(i);
            
        list<int>::iterator current = numbers.begin();
        while(numbers.size() > 1)
        {
            for(int i = 1; i < m; i++) { // 走m-1步到达第m个数处
                ++current;
                if(current == numbers.end())
                    current = numbers.begin();
            }
             
            list<int>::iterator next = ++current;
            if(next == numbers.end())
                next = numbers.begin();
                
            --current;
            numbers.erase(current);
            current = next;
        }
        return *current;
    }
};

// 链表写法2
class Solution
{
public:
    int LastRemaining_Solution(int n, int m)
    {
        if(n < 1||m < 1)
            return -1;
        
        list<int> nums;
        for(int i = 0; i < n; i++)
            nums.push_back(i);
        
        int i = 0;
        while(nums.size() > 1) {
            i = (i + m - 1) % nums.size(); // 出队的位置索引
            auto it = nums.begin();
            advance(it, i); // 从链头往后找i个位置
            nums.erase(it); // 删除第i个位置
        }
        return *nums.begin();
    }
};
```



### 面试题66 构建乘积数组

【[OJ](https://www.nowcoder.com/practice/94a4d381a68b47b7a8bed86f2975db46?tpId=13&tqId=11204&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/82/)】给定一个数组A[0,1,...,n-1]，请构建一个数组B[0,1,...,n-1]，其中B中的元素B[i]=A[0]\*A[1]\*...\***A[i-1]\*A[i+1]**\*...\*A[n-1]。不能使用除法，使用 O(1) 的空间。（注意：规定B[0] = A[1] \* A[2] \* ... \* A[n-1]，B[n-1] = A[0] \* A[1] \* ...\* A[n-2]）

```
输入：[1, 2, 3, 4, 5]
输出：[120, 60, 40, 30, 24]
```

**题解**：求的是数组 A[0,1,...,n-1] 中除掉 A[i] 后其他元素的乘积。

不妨定义C[i] =A[0] x A[1]x ··· xA[i- 1] , D[i]=A[i+ I] x ··· xA [n-2] xA [n-1]。C[i] 可以用自上而下的顺序计算出来，即 C[i] =C[i-1] x A [i-1] 。类似的，D[i]也可以用自下而上的顺序计算出来，即 D[i] =D[i+1] xA[i+1]。

写法1：

```cpp
class Solution {
public:
    vector<int> multiply(const vector<int>& A) {
        int length = A.size();
        vector<int> B(length);
        
        if(length != 0 ){
            B[0] = 1;
            //计算下三角连乘
            for(int i = 1; i < length; i++){
                B[i] = B[i-1] * A[i-1];
            }
            int temp = 1;
            //计算上三角
            for(int j = length-2; j >= 0; j--){
                temp *= A[j+1];
                B[j] *= temp;
            }
        }
        return B;
    }
};
```

写法2：

```cpp
class Solution {
public:
    vector<int> multiply(const vector<int>& A) {
        if(A.empty()) return vector<int>();
        
        int n = A.size();
        vector<int> res(n);
        for(int i = 0, p = 1; i < n; i++) {
            res[i] = p;
            p *= A[i];
        }
        for(int i = n - 1, p = 1; i >= 0; i--) {
            res[i] *= p;
            p *= A[i];
        }
        return res;
    }
};
```



## 链表

### 面试题6 从尾到头打印链表（核心思想：栈）

【[OJ](https://www.nowcoder.com/practice/d0267f7f55b3412ba93bd35cfa8e8035?tpId=13&tqId=11156&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/18/)】输入一个链表，按链表从尾到头的顺序返回一个ArrayList。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/f5792051-d9b2-4ca4-a234-a4a2de3d5a57.png" width="250px" /> </div>

```
输入：[1, 2, 3]
返回：[3, 2, 1]
```

**题解**：

方法1：**递归**，实际上使用的是栈，推荐

要逆序打印链表 1->2->3（3,2,1)，可以先逆序打印链表 2->3(3,2)，最后再打印第一个节点 1。而链表 2->3 可以看成一个新的链表，要逆序打印该链表可以继续使用求解函数，也就是在求解函数中调用自己，这就是递归函数。

注意：结果的保存需放在递归函数的后面，**类似于二叉树的后序遍历**。

```cpp
class Solution {
public:
    vector<int> res;
    vector<int> printListFromTailToHead(ListNode* head) {
        if(head) {
            printListFromTailToHead(head->next);
            res.push_back(head->val);
        }
        return res;
    }
};
```



方法2：使用栈

栈具有后进先出的特点，在遍历链表时将值按顺序放入栈中，最后出栈的顺序即为逆序。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/9d1deeba-4ae1-41dc-98f4-47d85b9831bc.gif" width="330px" /> </div>

```cpp
class Solution {
public:
    vector<int> printListFromTailToHead(ListNode* head) {
        vector<int> vec;
        stack<int> stk;
        
        while (head) { // 遍历链表并将值存入栈中
            stk.push(head->val);
            head = head->next;
        }        
        while(!stk.empty()) { // 获取栈顶元素到vector，出栈
            vec.push_back(stk.top()); stk.pop();
        }        
        return vec;
    }
};
```



方法3：翻转链表后遍历

```cpp
class Solution {
public:
    vector<int> printListFromTailToHead(ListNode* head) {
        ListNode *pre = NULL, *next = NULL;
        while(head) {
            next = head->next;
            head->next = pre;
            pre = head;
            head = next;
        }
        
        vector<int> res;
        head = pre; // 当前队头为pre
        while(head) {
            res.push_back(head->val);
            head = head->next;
        }
        return res;
    }
};
```



方法4：正向遍历后存入vector，然后翻转vector即可

```cpp
class Solution {
public:
    vector<int> printListReversingly(ListNode* head) {
        vector<int> res;
        while(head) {
            res.push_back(head->val);
            head = head->next;
        }
        reverse(res.begin(), res.end());
        return res;
    }
};
```



方法5：借助vector的insert方法模拟栈（不推荐，代码简单，但是每次插入都要移动整个数组的元素）

```cpp
class Solution {
public:
    vector<int> printListFromTailToHead(ListNode* head) {
        vector<int> res;
        ListNode* pNode = head;
        while(pNode) {
            res.insert(res.begin(), pNode->val);
            pNode = pNode->next;
        }
        return res;
    }
};
```



### 面试题18.1 在O(1)时间内删除链表节点（核心思想：指针操作）⭐️

【[AcWing](https://www.acwing.com/problem/content/85/)】给定单向链表的头指针和一个节点指针，定义一个函数在 0(1) 时间内删除该节点

```
输入：链表 1->4->6->8
      删掉节点：第2个节点即6（头节点为第0个节点）
输出：新链表 1->4->8
```

**题解**：

① 如果该待删节点不是尾节点，那么可以直接将下一个节点的值赋给该待删节点，然后令该待删节点指向下下个节点，再删除下一个节点，时间复杂度为 O(1)。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/1176f9e1-3442-4808-a47a-76fbaea1b806.png" width="550px" /> </div>



② 否则，就需要先遍历链表，找到待删节点的前一个节点，然后让前一个节点指向 null，再删除待删节点，时间复杂度为 O(N)。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/4bf8d0ba-36f0-459e-83a0-f15278a5a157.png" width="550px" /> </div>



综上，如果进行 N 次操作，那么大约需要操作节点的次数为 N-1+N=2N-1，其中 N-1 表示 N-1 个不是尾节点的每个节点以 O(1) 的时间复杂度操作节点的总次数，N 表示 1 个尾节点以 O(N) 的时间复杂度操作节点的总次数。(2N-1)/N ~ 2，因此该算法的平均时间复杂度为 O(1)。



这里不使用标准做法，当待删节点为尾节点时，直接将尾节点置为NULL。

```cpp
class Solution {
public:
    void deleteNode(ListNode* node) {
        if(!node) return;
        
        if(!node->next) node = NULL; // 若当前节点是尾节点，将当前节点置为空
        else {
            auto p = node->next; // 指向下一节点的指针
            node->val = p->val; // 将下一节点的值复制过来
            node->next = p->next; // 当前节点的下一节点指向下下一个节点
            delete p; // 删除下一节点
        }
    }
};
```



### 面试题18.2 删除链表中重复的节点（核心思想：递归/迭代）⭐️⭐️

【[OJ](https://www.nowcoder.com/practice/fc533c45b73a41b0b44ccba763f866ef?tpId=13&tqId=11209&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/27/)】在一个排序的链表中，存在重复的结点，请删除该链表中重复的结点，重复的结点不保留，返回链表头指针。 例如，链表1->2->3->3->4->4->5 处理后为 1->2->5

```
输入：1->2->3->3->4->4->5
输出：1->2->5

输入：1->1->1->2->3
输出：2->3
```

**题解**：

🥇方法1：迭代

因为此题中可能将头结点删除，如`[4,4,4]->[]`，因而需要创建一个虚拟头结点以便于处理。

设置两个指针p、q，p指向肯定不重复的节点，q起初指向p的下一个节点，两指针**中间区域（不包含pq本身）**为重复节点区域

- 若pq指向节点值相等，即pq间有重复元素，q后移到pq指向节点值不等为止，最后将p指向q
- 若pq指向节点值不等，则p向后移动一位，q下次循环开始时更新为p的下个节点（当然不更新也可以，第二个while会进行q的后移，效果一样）

```cpp
class Solution {
public:
    ListNode* deleteDuplication(ListNode* head) {
        ListNode *dummy = new ListNode(-1); // 因为可能删除头节点，创建虚拟头结点以便处理
        dummy->next = head; // 虚拟头结点在原头节点前一位
        
        // pq之间为重复的节点
        ListNode *p = dummy;
        // ListNode *q = p->next; // 仅此处初始化一次，while中不更新也是可以的
        while(p && p->next) {
            ListNode *q = p->next; // 每次循环开始q均需更新为指向p的下一节点
            while(q && p->next->val == q->val) q = q->next; // q往后移直到pq值不等
            if(p->next->next == q) p = p->next; // p与q之间无重复节点，p右移
            else p->next = q; // pq之间有重复节点，p指向q
        }
        return dummy->next; // 返回原头节点
    }
};
```



方法2：递归，分当前节点是否是重复节点两种情况，较难理解

- 是重复节点则跳过与当前节点相同的所有节点，并从第一个与当前节点不同的节点开始递归
- 不是重复节点则保留当前节点，从下一节点开始递归

```cpp
class Solution {
public:
    ListNode* deleteDuplication(ListNode* pHead)
    {
        if (pHead == nullptr || pHead->next == nullptr) { // 只有0个或1个结点，则返回
            return pHead;
        }
        if (pHead->val == pHead->next->val) { // 当前结点是重复结点
            ListNode *pNode = pHead->next;
            while (pNode && pNode->val == pHead->val) {
                // 跳过值与当前结点相同的全部结点,找到第一个与当前结点不同的结点
                pNode = pNode->next;
            }
            return deleteDuplication(pNode); // 从第一个与当前结点不同的结点开始递归
        } else { // 当前结点不是重复结点
            pHead->next = deleteDuplication(pHead->next); // 保留当前结点，从下一个结点开始递归
            return pHead;
        }
    }
};
```



### 面试题22 链表中倒数第k个结点（核心思想：双指针）⭐️

【[OJ](https://www.nowcoder.com/practice/529d3ae5a407492994ad2a246518148a?tpId=13&tqId=11167&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/32/)】输入一个链表，输出该链表中倒数第k个结点。`k >= 0`;  如果k大于链表长度，则返回 NULL;

```
输入：链表：1->2->3->4->5 ，k=2
输出：4
```

**题解**：设链表的长度为 N。设置两个指针 P1 和 P2，先让 P1 移动 K 个节点，则还有 N - K 个节点可以移动。此时让 P1 和 P2 同时移动，可以知道当 P1 移动到链表结尾时，P2 移动到第 N - K 个节点处，该位置就是倒数第 K 个节点。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/6b504f1f-bf76-4aab-a146-a9c7a58c2029.png" width="550px" /> </div>

**需要在快指针移动的同时判断链表长度是否小于 k ！！！**

```cpp
class Solution {
public:
    ListNode* FindKthToTail(ListNode* pListHead, unsigned int k) {
        if(pListHead == nullptr || k == 0) return NULL;
        
        ListNode *slow = pListHead; // 慢指针，先指向头结点
        ListNode *fast = pListHead; // 快指针，先指向头结点
        
        while(k--) { // 快指针先移动K个节点
            if(fast == nullptr) return NULL; // 未移动完K次，快指针已移动到链尾，说明链长不足k
            fast = fast->next;
        }        
        while(fast) { // 快指针移动k步后，开始移动慢指针，即慢指针比快指针慢k，当快指针移动到链尾时，慢指针指向倒数第k个元素
            fast = fast->next;
            slow = slow->next;
        }        
        return slow;
    }
};
```

**关键：**

```cpp
if(fast == nullptr) return NULL; // 未移动完K次，快指针已移动到链尾，说明链长不足k
```



### 面试题23  链表中环的入口节点（核心思想：双指针）⭐️

【[OJ](https://www.nowcoder.com/practice/253d2c59ec3e4bc68da16833f79a38e4?tpId=13&tqId=11208&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/86/)】给一个链表，若其中包含环，请找出该链表的环的入口结点，否则，输出null。

<img src="https://gitee.com//MrRen-sdhm/Images/raw/master/img/20200603165956.png" alt="image-20200603165952310" style="zoom:67%;" />

```
给定如上所示的链表：
[1, 2, 3, 4, 5, 6]
2
注意，这里的2表示编号是2的节点，节点编号从0开始。所以编号是2的节点就是val等于3的节点。
则输出环的入口节点3.
```

**题解**：使用快慢指针，快指针一次走两步，慢指针一次走一步，如果快指针可以一直指到 nullptr，则说明没有环，如果快慢指针相遇则说明有环；如果两指针相遇的时候，将快指针重新指向链表的头结点，但是现在是一次走一步，最终快慢指针一定会相遇，相遇的地方即是入环的节点；[详解](https://cyc2018.github.io/CS-Notes/#/notes/23.%20%E9%93%BE%E8%A1%A8%E4%B8%AD%E7%8E%AF%E7%9A%84%E5%85%A5%E5%8F%A3%E7%BB%93%E7%82%B9)

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/bb7fc182-98c2-4860-8ea3-630e27a5f29f.png" width="500px" /> </div>

```cpp
class Solution {
public:
    ListNode* EntryNodeOfLoop(ListNode* pHead)
    {
        ListNode *fast = pHead;
        ListNode *slow = pHead;
        while(fast && fast->next){
            fast = fast->next->next; // 快指针一次走两步
            slow = slow->next; // 慢指针一次走一步
            if(fast == slow) break;// 两指针在环内相遇
        }
        if(!fast || !fast->next) return NULL// fast指针指到链尾说明无环

        fast = pHead; // 确定有环，快指针指向链头
        while(fast != slow){ // 两指针一次走一步，直到相遇时，即为环入口
            fast = fast->next;
            slow = slow->next;
        }
        return fast;
    }
};
```



### 面试题24 反转链表（核心思想：指针操作/递归/迭代）

【[OJ](https://www.nowcoder.com/practice/75e878df47f24fdc9dc3e400ec6058ca?tpId=13&tqId=11168&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/33/)】输入一个链表，反转链表后，输出新链表的表头。

```
输入:1->2->3->4->5->NULL
输出:5->4->3->2->1->NULL
```

**题解**：

方法1：指针操作，无需创建新头结点

```cpp
class Solution {
public:
    ListNode* ReverseList(ListNode* pHead) {
        if(!pHead) return NULL;
        
        ListNode *pre = NULL;
        ListNode *next = NULL;
        while(pHead) {
            next = pHead->next; // 保存下一节点
            pHead->next = pre; // 修改当前结点指向
            // 为下一循环做准备
            pre = pHead;
            pHead = next;
        }
        return pre;
    }
};
```



方法2：迭代，使用头插法，需创建新头节点

头插法顾名思义是将节点插入到头部：在遍历原始链表时，将当前节点插入新链表的头部，使其成为第一个节点。

链表的操作需要维护后继关系，例如在某个节点 node1 之后插入一个节点 node2，我们可以通过修改后继关系来实现：

```cpp
node3 = node1.next;
node2.next = node3;
node1.next = node2;
```

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/58c8e370-3bec-4c2b-bf17-c8d34345dd17.gif" width="200px" /> </div>

为了能将一个节点插入头部，我们引入了一个叫头结点的辅助节点，该节点不存储值，只是为了方便进行插入操作。不要将头结点与第一个节点混起来，第一个节点是链表中第一个真正存储值的节点。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/0dae7e93-cfd1-4bd3-97e8-325b032b716f-1572687622947.gif" width="400px" /> </div>

```cpp
class Solution {
public:
    ListNode* ReverseList(ListNode* pHead) {
        if(!pHead) return NULL;
        ListNode *dummy = new ListNode(-1); 
        ListNode *next = NULL;
        while(pHead) {
            next = pHead->next;
           	pHead->next = dummy->next;
            dummy->next = pHead;
            pHead = next;
        }
        return dummy;
    }
};
```



方法3：递归

```cpp
 ListNode* ReverseList(ListNode* pHead) {
    if (!pHead || !pHead->next) return head;
    ListNode *next = pHead->next;
    head->next = NULL;
    ListNode *newHead = ReverseList(next);
    next->next = pHead;
    return newHead;
}
```



附自定义链表节点写法，手撕必备！⭐️⭐️

```cpp
#include <iostream>
using namespace std;

struct ListNode {
    ListNode* next;
    int val;
    ListNode(int x) : next(NULL), val(x) {}
};

ListNode* ReverseList(ListNode *head) {
    if(!head) return NULL;
    ListNode* pre = NULL, *next = NULL;
    while(head) {
        next = head->next;
        head->next = pre;
        pre = head;
        head = next;
    }
    return pre;
}

ListNode* CreatList() {
    ListNode *dummy = new ListNode(-1);
    ListNode *cur = dummy;
    
    int val;
    while(cin >> val)
    {
        ListNode *node = new ListNode(val);
        cur->next = node;
        cur = cur->next;
    }
	return dummy->next;
}

void TraverseList(ListNode* head) {
    if(!head) return;
    ListNode *cur = head;
    while(cur) {
        cout << cur->val << ' ';
        cur = cur->next;
    }
}

int main() {
    // 手动创建链表
    ListNode *node1 = new ListNode(1);
    ListNode *node2 = new ListNode(2);
    ListNode *node3 = new ListNode(3);
    node1->next = node2;
    node2->next = node3;
    node3->next = NULL;
    
    ListNode *Head = CreatList(); // 创建链表
    ListNode *head = ReverseList(Head); // 反转链表
    TraverseList(head); // 遍历链表
    
    return 0;
}
```



### 面试题25 合并两个排序的链表（核心思想：递归/迭代）⭐️⭐️

【[OJ](https://www.nowcoder.com/practice/d8b6b4358f774294a89de2a6ac4d9337?tpId=13&tqId=11169&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/34/)】输入两个单调递增的链表，输出两个链表合成后的链表，合成后的链表满足单调不减规则。

```
输入：1->3->5 , 2->4->5
输出：1->2->3->4->5->5
```

**题解**：

方法1：迭代，**推荐**

因为头结点不确定，创建虚拟头结点以便处理

1. 新建头部的保护结点dummy，设置cur指针指向dummy。
2. 若当前l1指针指向的结点的值val比l2指针指向的结点的值val小，则令cur的next指针指向l1，且l1后移；否则指向l2，且l2后移。
3. 然后cur指针按照上一部设置好的位置后移。
4. 循环以上步骤直到l1或l2为空。
5. 将剩余的l1或l2接到cur指针后边。

```cpp
class Solution {
public:
    ListNode* Merge(ListNode* l1, ListNode* l2) {
        ListNode* dummy = new ListNode(-1); // 创建虚拟节点，以便于处理
        ListNode* cur = dummy;
        while(l1 && l2) {
            if(l1->val <= l2->val) {
                cur->next = l1;
                l1 = l1->next;
            } else {
                cur->next = l2;
                l2 = l2->next;
            }
            cur = cur->next;
        }
        cur->next = l1 ? l1 : l2; // 处理未遍历完的节点
        return dummy->next;
    }
};
```



方法2：递归

```cpp
class Solution {
public:
    ListNode* Merge(ListNode* l1, ListNode* l2) {
        if(!l1) return l2; // 链表1为空, 返回链表2
        if(!l2) return l1; // 链表2为空, 返回链表1
        
        if(l1->val < l2->val) { // 链表1头元素小于链表2头元素
            l1->next = Merge(l1->next, l2); // 剩余的链表按同样的方法合并
            return l1;
        } else { // 链表2头元素小于链表1头元素
            l2->next = Merge(l1, l2->next); // 剩余的链表按同样的方法合并
            return l2;
        }
    }
};
```





### 面试题35 复杂链表的复制✏️

【[OJ](https://www.nowcoder.com/practice/f836b2c43afc4b35ad6adc41ec941dba?tpId=13&tqId=11178&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/89/) / [Leetcode](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)】请实现一个函数可以复制一个复杂链表。在复杂链表中，每个结点除了有一个指针指向下一个结点外，还有一个额外的指针指向链表中的任意结点或者null。

函数结束后原链表要与输入时保持一致。

**题解**：

<img src="https://gitee.com//MrRen-sdhm/Images/raw/master/img/20200720101058.png" alt="image-20200720101055453" style="zoom:67%;" />

第一步是复制原始链表上的每一个结点，并用next节点链接起来；

<img src="https://gitee.com//MrRen-sdhm/Images/raw/master/img/20200720101342.png" style="zoom:67%;" />

第二步将复制出来节点的random指针指向原节点的random节点的复制，即`p->next->random = p->random->next`

<img src="https://gitee.com//MrRen-sdhm/Images/raw/master/img/20200720101406.png" alt="image-20200720101400638" style="zoom:67%;" />

第三步把这个长链表拆分成两个链表：把奇数位置的结点用Next链接起来就是原始链表，偶数数值的则是复制链表。

<img src="https://gitee.com//MrRen-sdhm/Images/raw/master/img/20200720101420.png" alt="image-20200720101418895" style="zoom:67%;" />

```cpp
class Solution {
public:
    ListNode *copyRandomList(ListNode *head) {
        if(!head) return head;
        
        auto p = head;
        while(p) { // 在原链表各节点后复制新的节点
            auto np = new ListNode(p->val);
            auto next = p->next; // 保存下一个原节点，以便修改p的下一指向
            p->next = np; // 插入新节点
            np->next = next; // 插入新节点
            p = next; // p指向下一个原节点
        }
        
        p = head;
        while(p) { // 将新建节点的random指针指向原节点的random指针所指向节点的复制
            if(p->random) // 原节点有random节点才需处理
                p->next->random = p->random->next;
            p = p->next->next; // 跳到下一个原节点
        }
        
        auto dummy = new ListNode(-1);
        auto cur = dummy;
        p = head;
        while(p) { // 拆分新链表，复原原链表
            auto pre = p; // 保存前一个原节点位置
            cur->next = p->next; // 将新节点连接到新链表上
            cur = cur->next; // 指向新链表下一节点
            p = p->next->next; // 指向下一个要拆出的节点的原节点
            pre->next = p; // 复原原链表
        }
        return dummy->next;
    }
};

```



### 面试题52 两个链表的第一个公共节点

【[OJ](https://www.nowcoder.com/practice/6ab1d9a29e88450685099d45c9e31e46?tpId=13&tqId=11189&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/62/)】输入两个链表，找出它们的第一个公共结点。

```
给出两个链表如下所示：
A：        a1 → a2
                   ↘
                     c1 → c2 → c3
                   ↗            
B:     b1 → b2 → b3

输出第一个公共节点c1
```

**题解**：

设 A 的长度为 a + c，B 的长度为 b + c，其中 c 为尾部公共部分长度，可知 a + c + b = b + c + a。

当访问链表 A 的指针访问到链表尾部时，令它从链表 B 的头部重新开始访问链表 B；同样地，当访问链表 B 的指针访问到链表尾部时，令它从链表 A 的头部重新开始访问链表 A。这样就能控制访问 A 和 B 两个链表的指针能同时访问到交点。

while循环的退出条件为 p1 != p2，因为：

- 若两链表相交，则最终两指针均指向相交节点
- 若两链表不相交，则最终两指针均指向空(NULL)

```cpp
// 写法1
class Solution {
public:
    ListNode *findFirstCommonNode(ListNode *headA, ListNode *headB) {
        auto p1 = headA, p2 = headB;
        while(p1 != p2) { // 同时移动两指针，直到两指针相等
            if(p1) p1 = p1->next;
            else p1 = headB; // p1指向空，则转向链表B
            
            if(p2) p2 = p2->next;
            else p2 = headA; // p2指向空，则转向链表A
        }
    }
};

// 写法2
class Solution {
public:
    ListNode* FindFirstCommonNode( ListNode* pHead1, ListNode* pHead2) {
        auto p1 = pHead1, p2 = pHead2;
        while(p1 != p2) {
            p1 = (p1) ? p1->next : pHead2;
            p2 = (p2) ? p2->next : pHead1;
        }
        return p1;
    }
};
```



## 队列、堆、栈

### 面试题9 用两个栈实现队列（核心思想：双栈）

【[OJ](https://www.nowcoder.com/practice/54275ddae22f475981afa2244dd448c6?tpId=13&tqId=11158&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】用两个栈来实现一个队列，完成队列的Push和Pop操作。

**题解**：元素直接放入栈1，栈2为空时从栈1取出所有元素放入栈2

```cpp
class Solution
{
public:
    void push(int node) { // 元素直接插入栈1即可
        stack1.push(node);
    }

    int pop() {
        if(stack2.size() <= 0) { // 栈2为空，将栈1所有元素移入栈2
            while(stack1.size() > 0) {
                stack2.push(stack1.top());
                stack1.pop();
            }
        }
        
        //if(stack2.size() == 0)
            //throw new exception("queue is empty!");
        int head = stack2.top();
        stack2.pop();
        
        return head;
    }

private:
    stack<int> stack1;
    stack<int> stack2;
};
```



### 面试题30 包含min函数的栈（核心思想：双栈）

【[OJ](https://www.nowcoder.com/practice/4c776177d2c04c2494f2555c9fcc1e49?tpId=13&tqId=11173&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/90/)】定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数，时间复杂度为O(1)。

题解：元素直接压入主栈，仅当辅助栈为空或当前值小于等于辅助栈顶时，压入辅助栈。弹出时主栈栈顶元素等于辅助栈栈顶时，弹出辅助栈栈顶。

**题解**：

```cpp
class Solution {
public:
    stack<int> mstack;
    stack<int> help;
    
    void push(int value) {
        mstack.push(value); // 直接压入数据栈
        if(help.empty() || value <= help.top()) // 辅助栈为空或当前值小于等于辅助栈顶，压入辅助栈
            help.push(value);
    }
    void pop() {
        if(mstack.empty()) return;
        if(mstack.top() == help.top()) // 取出的值等于最小值，辅助栈弹出栈顶
            help.pop();
        mstack.pop();
    }
    int top() {
        return mstack.top();
    }
    int min() {
        return help.top();
    }
};
```



### 面试题31 栈的压入、弹出序列（核心思想：双指针/辅助栈）

【[OJ](https://www.nowcoder.com/practice/d77d11405cc7470d82554cb392585106?tpId=13&tqId=11174&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/40/)】输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）

```
输入：[1,2,3,4,5]
      [4,5,3,2,1]
输出：true
```

**题解**：使用一个辅助栈来模拟压入弹出操作，辅助栈中逐个压入push中元素并与pop当前元素比较，若相等则弹出，直至不等，若栈最后为空则弹出序列正确。

使用双指针i、j，i 负责逐个将压入序列的元素放入辅助栈，压入之后判断栈顶元素是否与弹出序列当前位置 j 指向的元素相等，相等则弹出，j 指针后移继续判断之后的弹出序列是否与压入序列匹配。

```cpp
class Solution {
public:
    bool IsPopOrder(vector<int> pushV,vector<int> popV) {
        if(pushV.size() != popV.size()) return false;
        stack<int> stk;
        for(int i = 0, j = 0; i < pushV.size(); i++) {
            stk.push(pushV[i]); // 逐个将pushV中元素压入辅助栈
            while(!stk.empty() && stk.top() == popV[j]) { // 当前辅助栈栈顶与popV下一元素相等
                stk.pop(); // 弹出辅助栈栈顶
                j++; // 继续与popV中下一个元素比较
            }
        }
        return stk.empty(); // 若辅助栈元素全部弹出，证明弹出序列可能
    }
};
```



### 面试题59.1 滑动窗口的最大值（核心思想：双端队列）⭐️

【[OJ](https://www.nowcoder.com/practice/1624bc35a45c42c0bc17d17fa0cba788?tpId=13&tqId=11217&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/75/)】给定一个数组和滑动窗口的大小，找出所有滑动窗口里数值的最大值。例如，如果输入数组{2,3,4,2,6,2,5,1}及滑动窗口的大小3，那么一共存在6个滑动窗口，他们的最大值分别为{4,4,6,6,6,5}； 针对数组{2,3,4,2,6,2,5,1}的滑动窗口有以下6个： {[2,3,4],2,6,2,5,1}， {2,[3,4,2],6,2,5,1}， {2,3,[4,2,6],2,5,1}， {2,3,4,[2,6,2],5,1}， {2,3,4,2,[6,2,5],1}， {2,3,4,2,6,[2,5,1]}。

```
输入：[2, 3, 4, 2, 6, 2, 5, 1] , k=3
输出: [4, 4, 6, 6, 6, 5]
```

**题解**：窗口向右滑动的过程实际上就是将处于窗口的第一个数字删除，同时在窗口的末尾添加一个新的数字，这就可以用双向队列来模拟，**每次把尾部的数字弹出，再把新的数字压入到头部**，然后找队列中最大的元素即可。

为了更快地找到最大的元素，我们可以在队列中**只保留那些可能成为窗口最大元素的数字**，去掉那些不可能成为窗口中最大元素的数字。考虑这样一个情况，如果队列中进来一个较大的数字，那么队列中比这个数更小的数字就不可能再成为窗口中最大的元素了，因为这个大的数字是后进来的，一定会比之前早进入窗口的小的数字要晚离开窗口，那么那些早进入且比较小的数字就“永无出头之日”，所以就可以弹出队列。

于是我们维护一个双向单调队列，**队列放的是可能最大的元素的下标**。我们假设该双端队列的队头是整个队列的最大元素所在下标，至队尾下标代表的元素值依次降低。初始时单调队列为空。随着对数组的遍历过程中，每次插入元素前，首先需要看队头是否还能留在队列中，如果队头下标距离 i 超过了k，则应该出队。同时需要维护队列的单调性，如果nums[i]大于或等于队尾元素下标所对应的值，则当前队尾再也不可能充当某个滑动窗口的最大值了，故需要队尾出队。始终保持队中元素从队头到队尾单调递减。依次遍历一遍数组，每次队头就是每个滑动窗口的最大值所在下标。

时间复杂度分析：每个元素最多入队出队一次，复杂度为O(n)

```cpp
class Solution {
public:
    vector<int> maxInWindows(vector<int>& nums, int k) {
        if(nums.empty()) return vector<int>();
        
        vector<int> res;
        deque<int> q;
        
        for(int i = 0; i < nums.size(); i++) {
            while(q.size() && q.front() <= i - k) q.pop_front(); // 若队头已不在窗口中，队头出队
            while(q.size() && nums[q.back()] <= nums[i]) q.pop_back(); // 维护队列单调性，队尾元素小于当前元素，不可能是最大值，弹出
            q.push_back(i);
            if(i >= k - 1) res.push_back(nums[q.front()]); // 窗口中有k个元素时开始，取队头作为窗口最大元素
        }
        return res;
    }
};
```



### 面试题41 数据流中的中位数（核心思想：堆）

【[OJ](https://www.nowcoder.com/practice/9be0172896bd43948f8a32fb954e1be1?tpId=13&tqId=11216&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/88/)】如何得到一个数据流中的中位数？如果从数据流中读出奇数个数值，那么中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，那么中位数就是所有数值排序之后中间两个数的平均值。我们使用Insert()方法读取数据流，使用GetMedian()方法获取当前读取数据的中位数。

**题解**：使用大顶堆维护左半边数组，使用小顶堆维护右半边数组。即大顶堆存放较小的一般元素，小顶堆存放较大的一般元素。

C++中使用堆，可利用STL中的[priority_queue](http://c.biancheng.net/view/480.html)

方法1：

```cpp
class Solution {
    priority_queue<int, vector<int>, less<int> > max; // 最大堆，最大的元素在队列前面， 存储左半边元素
    priority_queue<int, vector<int>, greater<int> > min;  // 最小堆，最大的元素在队列前面，存储右半边元素
     
public:
    // 大顶堆存储左半部分，大顶堆的堆顶应小于小顶堆的堆顶
    void Insert(int num){
        if(max.empty() || num <= max.top()) max.push(num); // 比大顶堆堆顶小的全放入大顶堆
        else min.push(num); // 比大顶堆堆顶大的放入小顶堆
        
        // 为保证两堆的平衡状态，大顶堆比小顶堆多两个元素时，将大顶堆堆顶移入小顶堆，允许大顶堆比小顶堆多一个元素
        if(max.size() == min.size() + 2) {
            min.push(max.top());
            max.pop();
        }
        
        // 为保证两堆的平衡状态，小顶堆比大顶堆多一个元素时，将小顶堆堆顶移入大顶堆，不允许小顶堆比大顶堆多一个元素
        if(max.size() + 1 == min.size()) {
            max.push(min.top());
            min.pop();
        }
    }
    
    double GetMedian(){
      // 偶数个元素时，两堆元素相等，中位数为两堆顶取平均；奇数个元素时，大顶堆比小顶堆多一个元素，中位数为大顶堆堆顶
      return max.size() == min.size() ? (max.top() + min.top()) / 2.0 : max.top();
    }
};
```



🥇方法2：

- 先将数字插入大根堆
- 大根堆中的元素应小于小根堆中的元素，当大根堆的堆顶大于小根堆的堆顶时，交换堆顶
- 若大根堆中元素比小根堆中元素多两个，则把大根堆的堆顶移到小根堆中

```c
class Solution {
public:
    priority_queue<int> max_heap;
    priority_queue<int, vector<int>, greater<int>> min_heap;

    void insert(int num){
        max_heap.push(num); // 插入大根堆
        if(min_heap.size() && min_heap.top() < max_heap.top()) // 逆序，应交换
        {
            auto minv = min_heap.top(), maxv = max_heap.top();
            min_heap.pop(), max_heap.pop();
            max_heap.push(minv), min_heap.push(maxv);
        }
        if(max_heap.size() > min_heap.size() + 1) // 大顶堆元素过多，把大顶堆堆顶放入小顶堆
        {
            min_heap.push(max_heap.top());
            max_heap.pop();
        }
    }

    double getMedian(){
        if (max_heap.size() + min_heap.size() & 1) return max_heap.top(); // 奇数个元素，返回大顶堆堆顶
        return (max_heap.top() + min_heap.top()) / 2.0; // 偶数个元素，返回平均值
    }
};
```



## 二叉树

### 面试题7 重建二叉树（核心思想：递归）⭐️⭐️

【[OJ](https://www.nowcoder.com/practice/8a19cbe657394eeaac2f6ea9b0f6fcf6?tpId=13&tqId=11157&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/image-20191102210342488.png" width="350px" /> </div>



**题解**：

方法1：递归，时间复杂度O(n^2^)。在中序遍历中查找根节点位置的操作，需要 O(n) 的时间。创建每个节点需要的时间是 O(1)，所以总时间复杂度是 O(n^2^)。

递归建立整棵二叉树：先递归创建左右子树，然后创建根节点，并让指针指向两棵子树。

具体步骤如下：

- 先利用前序遍历找根节点：**前序遍历的第一个数，就是根节点的值**；
- **在中序遍历中找到根节点的位置 k**，则 k 左边是左子树的中序遍历，右边是右子树的中序遍历；
- **左子树中序遍历的长度可利用 k 计算得到**。假设左子树的中序遍历的长度是 l，则在前序遍历中，根节点后面的 l 个数，是左子树的前序遍历，剩下的数是右子树的前序遍历；
- 有了左右子树的前序遍历和中序遍历，我们可以先递归创建出左右子树，然后再创建根节点；

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/60c4a44c-7829-4242-b3a1-26c3b513aaf0.gif" width="350px" /> </div>



- 左子树节点在先序遍历序列中的范围为[**startPre+1**,startPre+llen]
- 左子树节点在中序遍历序列中的范围为[startIn,i-1]
- 右子树节点在先序遍历序列中的范围为[startPre+llen+1,endPre]
- 右子树节点在中序遍历序列中的范围为[i+1,endIn]

```cpp
class Solution {
public:
    TreeNode* reConstructBinaryTree(vector<int> pre,vector<int> vin) {
        if(pre.empty() || vin.empty()) return NULL; // 检查是否为空
        return dfs(pre, vin, 0, pre.size()-1, 0, vin.size()-1);
    }
    
    TreeNode* dfs(vector<int>& pre, vector<int>& in, int pl, int pr, int il, int ir) {
        if(pl > pr || il > ir) return NULL; // 递归到了根节点
        TreeNode *root = new TreeNode(pre[pl]); // 创建根节点，根节点为先序序列首元素
        for(int i = il; i <= ir; i++) {
            if(in[i] == pre[pl]) { // 寻找根节点在中序遍历序列中的位置
                int llen = i - il; // 利用根节点在中序遍历序列中的位置计算左子树节点数
                root->left = dfs(pre, in, pl + 1, pl + llen, il, i-1);
                root->right = dfs(pre, in, pl + llen + 1, pr, i+1, ir);
                break;
            }
        }
        return root;
    }
};
```



方法2：使用哈希表保存前序与中序的对应关系，使得查找的时间复杂度降低到O(1)，总的时间复杂度是O(n)

```cpp
class Solution {
public:
    unordered_map<int,int> pos;
    TreeNode* reConstructBinaryTree(vector<int> pre,vector<int> vin) {
        int n = pre.size();
        for (int i = 0; i < n; i ++ )
            pos[vin[i]] = i;
        return dfs(pre, vin, 0, n - 1, 0, n - 1);
    }

    TreeNode* dfs(vector<int>& pre, vector<int>& in, int pl, int pr, int il, int ir)
    {
        if (pl > pr || il > ir) return NULL;
        int k = pos[pre[pl]] - il;
        TreeNode* root = new TreeNode(pre[pl]);
        root->left = dfs(pre, in, pl + 1, pl + k, il, il + k - 1);
        root->right = dfs(pre, in, pl + k + 1, pr, il + k + 1, ir);
        return root;
    }
};
```



### 面试题8 二叉树的下一个结点⭐️

【[OJ](https://www.nowcoder.com/practice/9023a0c988684a53960365b889ceaf5e?tpId=13&tqId=11210&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】给定一个二叉树和其中的一个结点，请找出中序遍历顺序的下一个结点并且返回。注意，树中的结点不仅包含左右子结点，同时包含指向父结点的指针。

**题解**：

① 如果一个节点的右子树不为空，那么该节点的下一个节点是右子树的最左节点；

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/7008dc2b-6f13-4174-a516-28b2d75b0152.gif" width="250px" /> </div>



② 否则，向上找第一个是其祖先节点的左子节点的节点，则下一个节点为其祖先节点。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/094e3ac8-e080-4e94-9f0a-64c25abc695e.gif" width="250px" /> </div>

```cpp
class Solution {
public:
    TreeLinkNode* GetNext(TreeLinkNode* p)
    {
        if(p->right) { // 存在右子节点，则右子树中最左侧的节点就是当前节点的后继
            p = p->right;
            while(p->left) p = p->left;
            return p;
        }
        while(p->next) { // 不存在右子节点，则向上找到第一个是其双亲左子节点的节点，该节点的双亲即是下一个节点
            if(p->next->left == p) return p->next;
            p = p->next;
        }
        return NULL;
    }
};
```



### 面试题26 树的子结构（核心思想：递归）⭐️

【[OJ](https://www.nowcoder.com/practice/6e196c44c7004d15b1610b9afca8bd88?tpId=13&tqId=11170&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】输入两棵二叉树A，B，判断B是不是A的子结构。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/84a5b15a-86c5-4d8e-9439-d9fd5a4699a1.jpg" width="400px" /> </div>

**题解**：

第一步：在A树中查找与B树根节点值相等的节点，递归

第二步：找到相等节点后，逐个判断相等节点左右子树中的各节点值是否相等， 递归

```cpp
class Solution {
public:
    bool HasSubtree(TreeNode* pRoot1, TreeNode* pRoot2)
    {      
        if(pRoot1 == nullptr || pRoot2 == nullptr) // 已到根节点
            return false;
        
        bool result = false;
        // 在A树中找到与B树根节点元素值相等的节点
        if(pRoot1->val == pRoot2->val) // A中找到相等节点，从此节点及B树中根节点开始遍历，判断各子节点是否相等
            result = DoesTree1HaveTree2(pRoot1, pRoot2);
        if(!result) // 未找到相等节点，或B树不属于A树相等节点开始的子树的子结构，进入A树左子树查找
            result = HasSubtree(pRoot1->left, pRoot2);
        if(!result) // 未找到相等节点，或B树不属于A树相等节点开始的子树的子结构，并且不是相等节点左子树的子结构，进入A树右子树查找
            result = HasSubtree(pRoot1->right, pRoot2);
        
        return result;
    }
    
    bool DoesTree1HaveTree2(TreeNode* pRoot1, TreeNode* pRoot2) {
        if(pRoot2 == nullptr) // B树当前节点已经是叶子节点，说明B是A的子结构
            return true;
        if(pRoot1 == nullptr) // B树当前节点不是叶子节点，而A树中当前节点为叶子节点，说明B不是A的子结构
            return false;
        if(pRoot1->val != pRoot2->val) // AB两树当前节点值不等，并且均非叶子节点，说明B不是A的子结构
            return false;
        
        // AB数中当前节点相等，判断下一个节点
        return(DoesTree1HaveTree2(pRoot1->left, pRoot2->left) &&
               DoesTree1HaveTree2(pRoot1->right, pRoot2->right));
    }
};
```



### 面试题27 二叉树的镜像（核心思想：递归）

【[OJ](https://www.nowcoder.com/practice/564f4c26aa584921bc75623e48ca3011?tpId=13&tqId=11171&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】操作给定的二叉树，将其变换为源二叉树的镜像。

```
源二叉树 
    	    8
    	   /  \
    	  6   10
    	 / \  / \
    	5  7 9  11
镜像二叉树
    	    8
    	   /  \
    	  10   6
    	 / \  / \
    	11 9 7   5
```

**题解**：先序遍历二叉树，交换左右子节点即可

```cpp
class Solution {
public:
    void Mirror(TreeNode *pRoot) {
        if(pRoot == nullptr) return; // 递归边界
        
        swap(pRoot->left, pRoot->right); // 交换左右子节点
        Mirror(pRoot->left); // 遍历左子树
        Mirror(pRoot->right); // 遍历右子树
    }
};
```



### 面试题28 对称的二叉树（核心思想：递归）⭐️

【[OJ](https://www.nowcoder.com/practice/ff05d44dfdb04e1d83bdbdab320efbcb?tpId=13&tqId=11211&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】请实现一个函数，用来判断一颗二叉树是不是对称的。注意，如果一个二叉树同此二叉树的镜像是同样的，定义其为对称的。

```
    	    8
    	   /  \
    	  6    6
    	 / \  / \
    	5  7  7  5
```

**题解**：同时遍历左子树与右子树，满足以下条件时为对称二叉树

条件1：左子树的左子节点与右子树的右子节点相等

条件2：左子树的右子节点与右子树的左子节点相等

```cpp
class Solution {
public:
    bool isSymmetrical(TreeNode* pRoot)
    {
        return isSymmetrical(pRoot, pRoot);
    }
    
    bool isSymmetrical(TreeNode* pRoot1, TreeNode* pRoot2) {
        if(pRoot1 == nullptr && pRoot2 == nullptr) // 同时遍历到叶子节点并且未出现节点值不相等
            return true;
        
        if(pRoot1 == nullptr || pRoot2 == nullptr) // 未同时遍历到叶子节点，说明左右子树节点数不等，不对称
            return false;
        
        if(pRoot1->val != pRoot2->val) // 两节点值不等，不对称
            return false;
        
        // 同时进行前序遍历和前序对称遍历
        return isSymmetrical(pRoot1->left, pRoot2->right) && isSymmetrical(pRoot1->right, pRoot2->left);
    }

};
```




### 面试题32.1 从上往下打印二叉树

【[OJ](https://www.nowcoder.com/practice/7fe2212963db4790b57431d9ed259701?tpId=13&tqId=11175&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/41/)】从上往下打印出二叉树的每个节点，同层节点从左至右打印。

**题解**：使用队列保存将要打印的节点，节点出队列时将其左右子节点加入队列

```cpp
class Solution {
public:
    // 通用写法，隐含分层思想，使用两个循环
	vector<int> PrintFromTopToBottom(TreeNode* root) {
        if(!root) return res;
        vector<int> res;
        queue<TreeNode*> nodeQueue;

        nodeQueue.push(root); // 头结点先入队，后续再循环打印
        while(!nodeQueue.empty()) {
            int cnt = nodeQueue.size();
            while(cnt-- > 0) { // 遍历某层节点的所有子节点
                root = nodeQueue.front(); // 使用root保存队头，节省一个指针变量
                nodeQueue.pop(); // 队头出队

                if(root == nullptr) // 此节点为null，不打印，也不添加其子节点
                    continue;
                res.push_back(root->val); // 打印头结点

                nodeQueue.push(root->left); // 左子节点，加入队列，即使是null
                nodeQueue.push(root->right); // 有右子节点，加入队列，即使是null
            }
        }

        return res;
    }

    // 不分层时最快解法，单个循环层序遍历
    vector<int> PrintFromTopToBottom_(TreeNode* root) {
        if(!root) return res;
        vector<int> res;
        queue<TreeNode*> nodeQueue;

        nodeQueue.push(root); // 头结点先入队，后续再循环打印
        while(!nodeQueue.empty()) {
            root = nodeQueue.front(); // 使用root保存队头，节省一个指针变量
            nodeQueue.pop(); // 队头出队
            res.push_back(root->val); // 打印头结点

            if(root->left) // 有左子节点，加入队列
                nodeQueue.push(root->left);
            if(root->right) // 有右子节点，加入队列
                nodeQueue.push(root->right);
        }

        return res;
    }
};
```



### 面试题32.2 把二叉树打印成多行

【[OJ](https://www.nowcoder.com/practice/445c44d982d04483b04a54f298796288?tpId=13&tqId=11213&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWIng](https://www.acwing.com/problem/content/42/)】从上到下按层打印二叉树，同一层结点从左至右输出。每一层输出一行。

```
输入如下图所示二叉树[8, 12, 2, null, null, 6, null, 4, null, null, null]
    8
   / \
  12  2
     /
    6
   /
  4

输出：[[8], [12, 2], [6], [4]]
```

**题解**：要求一行打印一层，因此需要在每层打印完时记录下一层要打印的节点个数

```cpp
class Solution {
public:    
	// 简单方法，使用两层循环
    vector<vector<int> > Print(TreeNode* pRoot) {
        if(!pRoot) return res;
        vector<vector<int>> res;
        queue<TreeNode*> nodeQueue;

        nodeQueue.push(pRoot); // 头结点先入队，后续再循环打印
        while(!nodeQueue.empty()) {
            int cnt = nodeQueue.size(); // 下一层的节点数量
            vector<int> vec; // 保存此行的节点
            while(cnt-- > 0) { // 遍历某层节点的所有子节点
                pRoot = nodeQueue.front(); // 使用root保存队头，节省一个指针变量
                nodeQueue.pop(); // 队头出队

                if(pRoot == nullptr) // 此节点为null，不打印，也不添加其子节点
                    continue;
                vec.push_back(pRoot->val); // 打印头结点

                nodeQueue.push(pRoot->left); // 左子节点，加入队列，即使是null
                nodeQueue.push(pRoot->right); // 有右子节点，加入队列，即使是null
            }

            if(!vec.empty()) // 此行有节点！！
                res.push_back(vec); // 打印此行节点
        }

        return res;
    }
};
```



### 面试题32.3 按之字形顺序打印二叉树

【[OJ](https://www.nowcoder.com/practice/91b69814117f4e8097390d107d2efbe0?tpId=13&tqId=11212&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) [AcWing](https://www.acwing.com/problem/content/43/)】请实现一个函数按照之字形打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右至左的顺序打印，第三行按照从左到右的顺序打印，其他行以此类推。

```
输入如下图所示二叉树[8, 12, 2, null, null, 6, 4, null, null, null, null]
    8
   / \
  12  2
     / \
    6   4
输出：[[8], [2, 12], [6, 4]]
```

**题解**：

**方法1**：使用queue和vector，将每行节点值保存到vector，需要逆序时使用reverse将vector逆序再打印

```cpp
class Solution {
public:
    vector<vector<int> > Print(TreeNode* pRoot) {
        vector<vector<int> > res;
        queue<TreeNode*> que;
        if(!pRoot) return res;
        
        bool zigZag = false; // 反转标志
        
        que.push(pRoot);
        while(!que.empty()) {
            int cnt = que.size();
            vector<int> vec; // 保存此行的节点
            while(cnt-- > 0) { // 遍历某层节点的所有子节点
                pRoot = que.front(); // 使用root保存队头，节省一个指针变量
                que.pop(); // 队头出队
                
                if(!pRoot) continue;
                
                vec.push_back(pRoot->val);
                
                que.push(pRoot->left); // 左子节点，加入队列，即使是null
                que.push(pRoot->right); // 右子节点，加入队列，即使是null
            }
            if(zigZag)
                reverse(vec.begin(), vec.end()); // 反转此行
            if(!vec.empty())
                res.push_back(vec);
            zigZag = !zigZag; // 切换反转标志
        }
        return res;
    }
};
```



方法2：使用deque，当需要反转时从队尾读取，保存到队头，并且先保存右子节点再保存左子节点

```cpp
class Solution {
public:
    // 简单方法，使用deque，为了区分打印顺序，使用deque而不是queue
    vector<vector<int> > Print(TreeNode* pRoot) {
        vector<vector<int>> res;
        deque<TreeNode*> nodeDeque;
        bool zigZag = false; // 左->右

        if(pRoot == nullptr) return res;

        nodeDeque.push_back(pRoot); // 头结点先入队，后续再循环打印

        while(!nodeDeque.empty()) {
            int cnt = nodeDeque.size();
            vector<int> vec; // 保存此行的节点
            while(cnt-- > 0) { // 遍历某层节点的所有子节点
                if(!zigZag) { // 左->右，前取后放，先存左后存右
                    pRoot = nodeDeque.front(); // 使用root保存队头，节省一个指针变量
                    nodeDeque.pop_front(); // 队头出队

                    if(!pRoot) continue; // 此节点为null，不打印，也不添加其子节点

                    nodeDeque.push_back(pRoot->left); // 左子节点，加入队列，即使是null
                    nodeDeque.push_back(pRoot->right); // 右子节点，加入队列，即使是null
                } else { // 右->左，后取前放，先存右后存左
                    pRoot = nodeDeque.back(); // 使用root保存队尾，节省一个指针变量
                    nodeDeque.pop_back(); // 队尾出队

                    if(!pRoot) continue; // 此节点为null，不打印，也不添加其子节点

                    nodeDeque.push_front(pRoot->right); // 右子节点，加入队列，即使是null
                    nodeDeque.push_front(pRoot->left); // 左子节点，加入队列，即使是null
                }

                vec.push_back(pRoot->val); // 打印头结点
            }

            zigZag = !zigZag; // 切换方向

            if(!vec.empty()) // 此行有节点！！
                res.push_back(vec); // 打印此行节点
        }

        return res;
    }
};
```



方法3：剑指offer解法，使用两个栈，较繁琐

```cpp
class Solution {
public:
    vector<vector<int> > Print_(TreeNode* pRoot) {
        vector<vector<int> > ret;
        if(!pRoot) // 检查
            return ret;

        std::stack<TreeNode *> levels[2];
        vector<int> tmp;
        int current = 0;
        int next = 1;

        levels[current].push(pRoot);

        while((!levels[0].empty()) || (!levels[1].empty())) {
            TreeNode *pNode = levels[current].top();
            levels[current].pop(); // 取出根节点

            //printf("%d ", pNode->val);
            tmp.push_back(pNode->val);

            if(current == 0) { // 偶数层，右孩子后入栈，下次先打印右孩子
                if(pNode->left != nullptr)
                    levels[next].push(pNode->left); // 存入另一个栈中

                if(pNode->right != nullptr)
                    levels[next].push(pNode->right);
            }
            else { // 奇数层，左孩子后入栈，下次先打印左孩子
                if(pNode->right != nullptr)
                    levels[next].push(pNode->right); // 存入另一个栈中

                if(pNode->left != nullptr)
                    levels[next].push(pNode->left);
            }

            if(levels[current].empty()) { // 当前栈中元素已打印，切换当前栈与另一个栈
                //printf("\n");
                ret.push_back(tmp); // 每次切换时保存一层
                tmp.clear();
                current = 1- current;
                next = 1- next;
            }
        }

        return ret;
    }
};
```



### 面试题33 二叉搜索树的后序遍历序列（核心思想：DFS）⭐️

【[OJ](https://www.nowcoder.com/practice/a861533d45854474ac791d90e447bafd?tpId=13&tqId=11176&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/description/44/)】输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则返回true，否则返回false。假设输入的数组的任意两个数字都互不相同。

```
输入：[4, 8, 6, 12, 16, 14, 10]
输出：true
```

**题解**：

二叉搜索树的后序遍历结果可以分为3段，最右侧元素为根节点的值，第一段为左子树的节点值，第二段为右子树的节点值，并且左子树节点值均小于根节点值，右子树节点值均大于根节点值。

先划分左子树这一段，再判断右子树这一段是否合法即可，若合法则继续递归判断左子树及右子树是否合法。

```cpp
class Solution {
public:
    vector<int> seq;
    bool VerifySquenceOfBST(vector<int> sequence) {
        seq = sequence;
        if(seq.empty()) return false;
        return dfs(0, seq.size() - 1);
    }
    
    bool dfs(int l, int r) {
        if(l >= r) return true; // 序列长度为0，合法
        int root = seq[r];
        int k = l;
        while(k < r && seq[k] < root) k++; // 找到中点位置，k左侧为左子树节点，k开始为右子树节点
        for(int i = k; i < r; i++) { // 判断右子树中节点值是否都大于根节点
            if(seq[i] < root) return false; // 节点值小于根节点，不合法
        }
        return dfs(l, k - 1) && dfs(k, r - 1); // 递归判断左子树和右子树
    }
};
```



### 面试题34 二叉树中和为某一值的路径（核心思想：DFS&回溯）

【[OJ](https://www.nowcoder.com/practice/b736e784e3e34731af99065031301bca?tpId=13&tqId=11177&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/45/)】输入一颗二叉树的根节点和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。

```
给出二叉树如下所示，并给出num=22。
      5
     / \
    4   6
   /   / \
  12  13  6
 /  \    / \
9    1  5   1

输出：[[5,4,12,1],[5,6,6,5]]
```

**题解**：

DFS+回溯

写法1：

```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;
    vector<vector<int>> FindPath(TreeNode* root, int sum) {
        if(!root) return res;
        path.push_back(root->val); // 添加当前节点到路径
        
        if(!root->left && !root->right && root->val == sum) // 搜索到叶子节点，判断路径和
            res.push_back(path);
        
        FindPath(root->left, sum - root->val); // 进入左子树搜索
        FindPath(root->right, sum - root->val); // 进入右子树搜索
        
        path.pop_back(); // 回溯
        return res;
    }
};
```



写法2：

```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<int> path;
    vector<vector<int>> FindPath(TreeNode* root, int sum) {
        dfs(root, sum);
        return res;
    }
    
    void dfs(TreeNode* root, int sum) {
        if(!root) return;
        path.push_back(root->val); // 添加当前节点到路径
        
        if(!root->left && !root->right && root->val == sum) // 搜索到叶子节点，判断路径和
            res.push_back(path);
        
        FindPath(root->left, sum - root->val); // 进入左子树搜索
        FindPath(root->right, sum - root->val); // 进入右子树搜索
        
        path.pop_back(); // 回溯
    }
};
```



### 面试题36 二叉搜索树与双向链表⚔️

【[OJ](https://www.nowcoder.com/practice/947f6eb80d944a84850b0538bf0ec3a5?tpId=13&tqId=11179&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

```cpp
class Solution {
public:
    TreeNode* Convert(TreeNode* pRootOfTree)
    {
        if(pRootOfTree == nullptr)
            return nullptr;
        
        TreeNode* pre = nullptr; // 指向前一个节点，即左子节点
           
        inOrder(pRootOfTree, pre); // 利用中序遍历进行转换
           
        TreeNode* res = pRootOfTree; // 获取尾节点
        while(res->left) // 获取头结点
            res = res->left;
        
        return res;
    }
       
    void inOrder(TreeNode* root, TreeNode*& pre) // 注意：pre指针需要传引用，对其进行修改
    {
        if(root == nullptr) return; // 叶子节点
        
        inOrder(root->left, pre); // 转换左子树
        
        // 转换双亲节点
        root->left = pre;
        if(pre) pre->right = root;
        pre = root;
           
        inOrder(root->right, pre); // 转换右子树
    }
};
```



### 面试题37 序列化二叉树⚔️

【[OJ](https://www.nowcoder.com/practice/cf7e25aa97c04cc1a68c8f040e71fb84?tpId=13&tqId=11214&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

```cpp
class Solution {
public:
    char* Serialize(TreeNode *root) {   
        if(!root) return "#";
        string r = to_string(root->val);
        r.push_back(',');
        char *left = Serialize(root->left);
        char *right = Serialize(root->right);
        char *ret = new char[strlen(left) + strlen(right) + r.size()];
        strcpy(ret, r.c_str());
        strcat(ret, left);
        strcat(ret, right);
        return ret;
    }
    
    TreeNode* Deserialize(char *str) {
        return decode(str);
    }
    
    TreeNode* decode(char *&str) {
        if(*str=='#'){
            str++;
            return NULL;
        }
        int num = 0;
        while(*str != ',')
            num = num*10 + (*(str++)-'0');
        str++;
        TreeNode *root = new TreeNode(num);
        root->left = decode(str);
        root->right = decode(str);
        return root;
    }
};
```



### 面试题55.1 二叉树的深度

【[OJ](https://www.nowcoder.com/practice/435fb86331474282a3499955f0a41e8b?tpId=13&tqId=11191&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

```cpp
class Solution {
public:
    // 递归统计左右子树的深度，返回最大深度
    int TreeDepth(TreeNode* pRoot)
    {
        if (pRoot == nullptr)
            return 0;
        
        int nLeft = TreeDepth(pRoot->left);
        int nRight = TreeDepth(pRoot->right);
        
        return (nLeft > nRight) ? (nLeft + 1) : (nRight + 1);
    }
};
```



### 面试题55.2 平衡二叉树

【[OJ](https://www.nowcoder.com/practice/8b3b95850edb4115918ecebdf1b4d222?tpId=13&tqId=11192&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

后序遍历，并在遍历左右子树的同时，实时记录并比较左右子树的深度

```cpp
class Solution {
public:
    bool IsBalanced_Solution(TreeNode* pRoot) {
        int depth = 0;
        return IsBanlanced(pRoot, &depth);
    }
    
    bool IsBanlanced(TreeNode* pRoot, int* depth) {
        if(!pRoot) {
            *depth = 0;
            return true;
        }
        
        int left_depth = 0, right_depth = 0;
        
        if(IsBanlanced(pRoot->left, &left_depth) && // 后序遍历
            IsBanlanced(pRoot->right, &right_depth)) {
            int diff = left_depth - right_depth;
            if(diff <= 1 && diff >= -1) { // 当前二叉树平衡
                *depth = 1 + (left_depth > right_depth ? left_depth : right_depth); // 深度加1
                return true;
            }
        }
        
        return false;
    }
};
```



### 面试题54 二叉搜索树的第K大节点

【[OJ](https://www.nowcoder.com/practice/ef068f602dde4d28aab2b210e859150a?tpId=13&tqId=11215&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

二叉搜索树的中序遍历结果即从小到大依次排列

```cpp
class Solution {
public:
    TreeNode* res = nullptr;
    TreeNode* KthNode(TreeNode* pRoot, int k)
    {
        InOrder(pRoot, k); // 中序遍历
        return res;
    }
    
    void InOrder(TreeNode* pRoot, int &k) {
        if(!pRoot) // 遍历到叶子节点的子节点
            return;
        
        InOrder(pRoot->left, k); // 遍历左子树
        
        // 遍历根节点
        --k; // 遍历到不为空的节点时，--k，即已遍历节点个数加一
        if(k == 0)
            res = pRoot;
        
        InOrder(pRoot->right, k); // 遍历右子树
    }
};
```



## 字符串

### 面试题5 替换空格（核心思想：双指针）

【[OJ](https://www.nowcoder.com/practice/4060ac7e3e404ad1a894ef3e17650423?tpId=13&tqId=11155&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/17/)】请实现一个函数，将一个字符串中的每个空格替换成“%20”。

```
输入："We are happy."
输出："We%20are%20happy."
```

**题解**：使用双指针，时间复杂度O(n)

1. 首先遍历一遍原数组，求出最终答案的长度length；
2. 使用两个指针，指针i指向原字符串的末尾，指针 j 指向length的位置；
3. 两个指针分别从后往前遍历，如果*end == ' '，则指针j的位置上依次填充'0', '2', '%'，这样倒着看就是"%20"；如果str[i] != ' '，则指针j的位置上填充该字符即可。

由于 i 之前的字符串，在变换之后，长度一定不小于原字符串，所以遍历过程中一定有i <= j，这样可以保证str[j]不会覆盖还未遍历过的str[i]，从而答案是正确的。

```cpp
class Solution {
public:
    void replaceSpace(char *str,int length) {
        int lenNew = 0, len = 0;
        for (int i = 0; str[i] != '\0'; i++) { // 统计字符串实际长度及空格数
            len++;
            if (str[i] == ' ') {
                lenNew += 3;
            } else lenNew++;
        }
        if(len + 1 > length) return; // 输入的length包含'\0'的长度
        
        int end = len, endNew = lenNew; // 从'\0开始替换'
        while(end >= 0) {
            if(str[end] != ' ') str[endNew--] = str[end];
            else { // 替换空格
                str[endNew--] = '0';
                str[endNew--] = '2';
                str[endNew--] = '%';
            }
            end--;
        }
    }
};
```



### 面试题19 正则表达式匹配

【[OJ](https://www.nowcoder.com/practice/45327ae22b7b413ea21df13ee7d6429c?tpId=13&tqId=11205&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】请实现一个函数用来匹配包括'.'和'*'的正则表达式。模式中的字符'.'表示任意一个字符，而'*'表示它前面的字符可以出现任意次（包含0次）。 在本题中，匹配是指字符串的所有字符匹配整个模式。例如，字符串"aaa"与模式"a.a"和"ab*ac*a"匹配，但是与"aa.a"和"ab*a"均不匹配

```cpp
class Solution {
public:
    bool match(char* str, char* pattern)
    {
        if(*str=='\0' && *pattern=='\0')
            return true;
        if(*str!='\0' && *pattern=='\0')
            return false;
        if(*(pattern+1)!='*'){
            if(*str==*pattern || (*str!='\0' && *pattern=='.'))
                return match(str+1,pattern+1);
            else return false;
        }
        else{
            if(*str==*pattern || (*str!='\0' && *pattern=='.'))
                return match(str,pattern+2) || match(str+1,pattern);
            else return match(str,pattern+2);
        }
    }
};
```



### 面试题20 表示数值的字符串

【[OJ](https://www.nowcoder.com/practice/6f8c901d091949a5837e24bb82a731f2?tpId=13&tqId=11206&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】请实现一个函数用来判断字符串是否表示数值（包括整数和小数）。例如，字符串"+100","5e2","-123","3.1416"和"-1E-16"都表示数值。 但是"12e","1a3.14","1.2.3","+-5"和"12e+4.3"都不是。



字符串"+100","5e2","-123","3.1416"和"-1E-16"都表示数值。 但"12e","1a3.14","1.2.3","+-5"和"12e+4.3"都不是。

- e后面一定要接数字
- 不能同时存在两个e
- 第一次出现+-符号，且不是在字符串开头，则必须紧接在e之后
- 第二次出现+-符号，则必须紧接在e之后
- e后面不能接小数点，小数点不能出现两次
- 不可有不合法字符

```cpp
class Solution {
public:
    bool isNumeric(char* str) {
        // 标记符号、小数点、e是否出现过
        bool sign = false, decimal = false, hasE = false;
        for (int i = 0; i < strlen(str); i++) {
            if (str[i] == 'e' || str[i] == 'E') {
                if (i == strlen(str) - 1) return false; // e后面一定要接数字
                if (hasE) return false;  // 不能同时存在两个e
                hasE = true;
            } else if (str[i] == '+' || str[i] == '-') {
                // 第二次出现+-符号，则必须紧接在e之后
                if (sign && str[i-1] != 'e' && str[i-1] != 'E') return false;
                // 第一次出现+-符号，且不是在字符串开头，则也必须紧接在e之后
                if (!sign && i > 0 && str[i-1] != 'e' && str[i-1] != 'E') return false;
                sign = true;
            } else if (str[i] == '.') {
                // e后面不能接小数点，小数点不能出现两次
                if (hasE || decimal) return false;
                decimal = true;
            } else if (str[i] < '0' || str[i] > '9') // 不合法字符
                return false;
        }
        return true;
    }
};
```



### 面试题48 最长不含重复字符的子字符串（核心思想：双指针/哈希）⭐️⭐️

【[AcWing](https://www.acwing.com/problem/content/57/) / [Leetcode](https://leetcode-cn.com/problems/zui-chang-bu-han-zhong-fu-zi-fu-de-zi-zi-fu-chuan-lcof/)】请从字符串中找出一个最长的不包含重复字符的子字符串，计算该最长子字符串的长度。假设字符串中只包含从’a’到’z’的字符。

```
输入："abcabc"
输出：3
```

**题解**：

题目中要求答案必须是 子串 的长度，意味着子串内的字符在原字符串中一定是连续的。因此我们可以将答案看作原字符串的一个滑动窗口，并维护窗口内不能有重复字符，同时更新窗口的最大值。

方法1：使用unordered_map，时间复杂度O(2n)

使用**双指针维护一个不含重复字符的字符串**，**通过哈希表判重**（统计各字符个数）。右侧指针每次右移一位尝试扩充不重复字符串，若增加的字符出现两次，则左侧指针移到右指针处，移动过程中将路过的字符从哈希表中移除。如12344，i 指向1，j 指向第二个4，则 i 每次右移一位，将123和第一个4从哈希表移除，此时 ij 均指向第二个4，这时开始下一次字符扩充。

<img src="https://gitee.com//MrRen-sdhm/Images/raw/master/img/20200601153204.gif" style="zoom: 67%;" />

```cpp
class Solution {
public:
    int longestSubstringWithoutDuplication(string s) {
        unordered_map<char, int> cnt;
        int res = 0;
        for(int i = 0, j = 0; j < s.size(); j++) { // 指针ij之间维护一个不重复字符串
            if(++cnt[s[j]] > 1) { // 将右侧指针指向的字符加入哈希表，若被加入字符的数量超过1
                while(cnt[s[i]] == 1) cnt[s[i++]]--; // 将i指针后移，移到第一个重复字符处，移除经过的字符
                cnt[s[i++]]--; // i指针再右移一次，移到第二个重复字符处，即j处
            }
            res = max(res, j - i + 1);
        }
        return res;
    }
};
```



方法2：使用数组取代哈希表，时间复杂度O(2n)

因为字符串仅由小写字母组成，使用数组模拟哈希表

```cpp
class Solution {
public:
    int longestSubstringWithoutDuplication(string s) {
        int cnt[256] = {0}; // 注意初始化
        int res = 0;
        for(int i = 0, j = 0; j < s.size(); j++) { // 扩大区间
            cnt[s[j]]++; // 扩大区间，出现次数加1
            while(cnt[s[j]] > 1) cnt[s[i++]]--; // 出现两次，缩小区间
            res = max(res, j - i + 1);
        }
        return res;
    }
};
```



### 面试题38 字符串的排列（核心思想：DFS&回溯）

【[OJ](https://www.nowcoder.com/practice/fe6b651b66ae47d7acce78ffdd9a96c7?tpId=13&tqId=11180&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/47/)】输入一个字符串,按字典序打印出该字符串中字符的所有排列。例如输入字符串abc,则打印出由字符a,b,c所能排列出来的所有字符串abc,acb,bac,bca,cab和cba。输入的字符串**可能有重复字符**。

从集合中依次选出每一个元素，作为排列的第一个元素，然后对剩余的元素进行全排列，如此递归处理，从而得到所有元素的全排列。以对字符串abc进行全排列为例，我们可以这么做：以abc为例

- 固定a，求后面bc的排列：abc，acb，求好后，a和b交换，得到bac
- 固定b，求后面ac的排列：bac，bca，求好后，c放到第一位置，得到cba
- 固定c，求后面ba的排列：cba，cab。

```
输入：[1,2,3]
输出：
     [[1,2,3], [1,3,2], [2,1,3], [2,3,1], [3,1,2], [3,2,1]]
```

**题解**：

此题和[重复数字全排列](https://leetcode-cn.com/problems/permutations-ii/)的本质一样，通过DFS+回溯+剪枝来进行求解。注意不可有连续两个字符重复，需要进行剪枝。例如aab的全排列为aab,aba,baa。

此题**可能有重复字符**，需要先进行排序，然后再进行剪枝

🥇方法1：DFS回溯+剪枝，通过记录路径+备忘录

写法1：

```cpp
class Solution {
public:
    vector<bool> used;
    string path;
    vector<string> res;

    vector<string> Permutation(string str) {
        if(str.empty()) return res;
        used = vector<bool>(str.size(), false);
        path = str;
        sort(str.begin(), str.end()); // 排序
        dfs(0, str); // 从第0层开始搜索
        return res;
    }

    void dfs(int cur_pos, string& str) {
        if(cur_pos == str.size()) { // 遍历到最后一层
            res.push_back(path);
            return;
        }
        for(int i = 0; i < str.size(); i++) { // 枚举当前层可以使用的字符，每个可使用的数会形成一条分支
            if(used[i]) continue; // 找到一个当前层没有用过的字符
            // 剪枝避免重复，若当前字符等于前一个字符并且前一个字符的选择被撤销，跳过
            if(i != 0 && !used[i - 1] && str[i] == str[i - 1]) continue;
            path[cur_pos] = str[i];
            
            used[i] = true; // 此字符已使用
            dfs(cur_pos + 1, str);
            used[i] = false; // 回溯-恢复现场，接下来将遍历另一条路径，此字符可重新使用
        }
    }
};
```



写法2：保存路径使用push_back 和 pop_back

```cpp
class Solution {
public:
    vector<bool> used;
    string path;
    vector<string> res;

    vector<string> Permutation(string str) {
        if(str.empty()) return res;
        used = vector<bool>(str.size(), false);
        sort(str.begin(), str.end()); // 排序
        dfs(0, str); // 从第0层开始搜索
        return res;
    }

    void dfs(int cur_pos, string& str) {
        if(cur_pos == str.size()) { // 遍历到最后一层
            res.push_back(path);
            return;
        }
        for(int i = 0; i < str.size(); i++) { // 枚举当前层可以使用的字符，每个可使用的数会形成一条分支
            if(used[i]) continue; // 找到一个当前层没有用过的字符
            // 剪枝避免重复，若当前字符等于前一个字符并且前一个字符的选择被撤销，跳过
            if(i != 0 && !used[i - 1] && str[i] == str[i - 1]) continue;
            
            path.push_back(str[i]);
            used[i] = true; // 此字符已使用
            dfs(cur_pos + 1, str);
            used[i] = false; // 回溯-恢复现场，接下来将遍历另一条路径，此字符可重新使用
            path.pop_back();
        }
    }
};
```



方法2：DFS回溯+剪枝，通过交换省去备忘录和路径记录

```cpp
class Solution {
public:
    vector<string> res;
    vector<string> Permutation(string str) {
        if(str.empty())
            return res;
        
        Permutation(str, 0);
        
        sort(res.begin(), res.end()); // 输出结果排序
        return res;
    }
    
    void Permutation(string str, int begin) {
        if(str[begin] == '\0') { // 递归边界，首字符已与所有字符进行了交换
            res.push_back(str);
        } else {
            for(int p = begin; str[p] != '\0'; ++p) { // 首字符与所有字符进行交换
                if(p != begin && str[begin] == str[p]) // 避免重复，如"aa" "a"
                    continue;
                
                swap(str[p], str[begin]); // 与后面的字符交换
                Permutation(str, begin + 1); // 后面的字符再进行递归
                swap(str[p], str[begin]); // 递归完成后恢复回未交换状态
            }
        }
    }
};
```



###  面试题50.1 字符串中第一个只出现一次的字符（核心思想：哈希表）

【[OJ](https://www.nowcoder.com/practice/1c82e8cf713b4bbeb2a5b31cf5b0417c?tpId=13&tqId=11187&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/59/)】在一个字符串(0<=字符串长度<=10000，全部由字母组成)中找到第一个只出现一次的字符,并返回它的位置, 如果没有则返回 -1（需要区分大小写）

```
输入："abaccdeff"
输出：'b'
```

**题解**：

最直观的解法是使用哈希表对出现次数进行统计，但是考虑到要统计的字符范围有限，因此可以使用char型数组代替哈希表，从而将空间复杂度由 O(N) 降低为 O(1)。

```cpp
class Solution {
public:
    int FirstNotRepeatingChar(string str) {
        if(str.empty()) return -1;
        
        char ch[256]={0}; // 使用一字节的char数组保存各字符出现的次数
        for(auto c : s) ch[str[i]]++;// 统计各字符出现次数
        for(auto c : s) if(ch[str[i]] == 1) return i; // 遍历次数数组，找到次数为1的位置
        return 0;
    }
};
```



### 面试题50.2 字符流中第一个不重复的字符

【[OJ](https://www.nowcoder.com/practice/00de97733b8e4f97a3fb5c680ee10720?tpId=13&tqId=11207&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】请实现一个函数用来找出字符流中第一个只出现一次的字符。例如，当从字符流中只读出前两个字符"go"时，第一个只出现一次的字符是"g"。当从该字符流中读出前六个字符“google"时，第一个只出现一次的字符是"l"。

**题解**：

**方法1**：使用queue保存出现过的字符，使用数组保存各字符出现的次数，判断队头是否只出现一次，时间复杂度O(1)，空间复杂度O(n)

1. 用一个128大小的数组统计每个字符出现的次数 
2. 用一个队列，如果第一次遇到ch字符，则插入队列；其他情况不在插入 
3. 求解第一个出现的字符，判断队首元素是否只出现一次，如果是直接返回，否则删除继续第3步骤

**方法2**：使用数组作为哈希表，统计各字符出现次数，时间复杂度O(n)，空间复杂度O(n)

```cpp
// 使用queue保存出现过的字符，使用数组保存各字符出现的次数，判断队头是否只出现一次
class Solution
{
public:
    queue<char> data; // 按序存放出现的字符，不重复
    char cnt[128] = {0}; // 统计各字符出现的次数
    
    //Insert one char from stringstream
    void Insert(char ch)
    {
        cnt[ch]++; // 此字符出现次数加1
        if(cnt[ch] == 1) // 此字符仅出现一次
            data.push(ch); // 放入队列
    }
    
    //return the first appearence once char in current stringstream
    char FirstAppearingOnce()
    {
        while (!data.empty() && cnt[data.front()] > 1) // 判断队头字符是否总共出现一次
            data.pop(); // 队头字符出现多次，取出，继续判断下一个字符
        
        return data.empty() ? '#' : data.front();
    }
};

// 使用数组作为哈希表，统计各字符出现次数
class Solution_
{
public:
    string s;
    char hash[256] = {0};
    
    //Insert one char from stringstream
    void Insert(char ch)
    {
        s += ch; // 加入字符流
        hash[ch]++; // 此字符出现次数加1
    }
    
    //return the first appearence once char in current stringstream
    char FirstAppearingOnce()
    {
        for(int i = 0; i < s.size(); ++i) { // 查找第一个出现一次的字符
            if(hash[s[i]] == 1)
                return s[i];
        }
        return '#';
    }
};
```



### 面试题58.1 翻转单词顺序（核心思想：双指针）

【[OJ](https://www.nowcoder.com/practice/3194a4f4cf814f63919d0790578d51f3?tpId=13&tqId=11197&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/73/)】输入一个英文句子，翻转句子中单词的顺序，但单词内字符的顺序不变。为简单起见，标点符号和普通字母一样处理。例如输入字符串`"I am a student."`，则输出`"student. a am I"`。

```
输入："I am a student."
输出："student. a am I"
```

**题解**：

先翻转整个字符串，再分别翻转以空格分割的子字符串即可

🥇写法1：简洁写法，技巧性较强

```cpp
class Solution {
public:
    string reverseWords(string s) {
        if(s.empty()) return s;
        reverse(s.begin(), s.end()); // 先翻转整个字符串
        for (int i = 0, j = 0; i < s.size(); i ++ ) {
            j = i; // j从下一个字符串开始找空格
            while (j < s.size() && s[j] != ' ') j++; // 退出循环时j指向空格
            reverse(s.begin() + i, s.begin() + j); // 翻转[s[i],s[j])，不包括s[j]
            i = j; // i指向空格，之后for循环中会i++，下次循环指向空格之后
        }
        return s;
    }
};
```



写法2：需要特殊处理最后一段字符串

```cpp
class Solution {
public:
    string reverseWords(string s) {
        if(s.empty()) return s;
        reverse(s, 0, s.size() - 1); // 翻转整个字符串
        for(int i = 0, j = 0; j < s.size(); j++) { // i指向无空格字符串的开始，第一段字符串从0开始，j寻找空格
            if(s[j] == ' ') { // 遇到空格
                reverse(s, i, j - 1); // 翻转开始与空格之前的字符串
                i = j + 1; // 下一段字符串开始为空格之后
            }
            if(j == s.size() - 1) // 处理最后一段字符串，因为最后一段字符串的结尾不是空格
                reverse(s, i, j);
        }
        return s;
    }
    
    void reverse(string &s, int l, int r) {
        while(l < r) swap(s[l++] , s[r--]);
    }
};
```



### 面试题58.2 左旋转字符串

【[OJ](https://www.nowcoder.com/practice/12d959b108cb42b1ab72cef4d36af5ec?tpId=13&tqId=11196&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】汇编语言中有一种移位指令叫做循环左移（ROL），现在有个简单的任务，就是用字符串模拟这个指令的运算结果。对于一个给定的字符序列S，请你把其循环左移K位后的序列输出。例如，字符序列S=”abcXYZdef”,要求输出循环左移3位后的结果，即“XYZdefabc”。是不是很简单？OK，搞定它！

以“abcdefg”为例，可以把它分为两部分。由于想把它的前两个字符移到后面，我们就把前两个字符分到第一部分，把后面的所有字符分到第二部分。我们先分别翻转这两部分，于是就得到“bagfedc”。接下来翻转整个字符串，得到的"cdefgab“刚好就是把原始字符串左旋转两位的结果。

```cpp
class Solution {
public:
    string LeftRotateString(string str, int n) {
        int len = str.size();
        if(len == 0 || n <= 0 || n > len)
            return str;
        
        Reverse(str, 0, n - 1); // 翻转前n个字符区域
        Reverse(str, n, str.size() - 1); // 翻转剩余区域
        Reverse(str, 0, str.size() - 1); // 翻转整个字符串
        
        return str;
    }
    
    void Reverse(string &str, int i, int j) {
        while(i < j) {
            swap(str[i++], str[j--]);
        }
    }
};

// 暴力解法 使用queue
class Solution_ {
public:
    string LeftRotateString(string str, int n) {
        queue<char> qu;
        
        for(int i = n; i < str.size(); ++i) {
            qu.push(str[i]);
        }
        for(int i = 0; i < n; ++i) {
            qu.push(str[i]);
        }
        for(int i = 0; i < str.size(); ++i) {
            str[i] = qu.front();
            qu.pop();
        }
        
        return str;
    }
};
```



### 面试题67 把字符串转换成整数

【[OJ](https://www.nowcoder.com/practice/1277c681251b4372bdef344468e4f26e?tpId=13&tqId=11202&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

需要考虑的情况：空字符串、正负号、非数字字符、整型数据溢出等

最大的正整数值是0x7FFF FFFF，最小的负整数是0x8000 0000

```cpp
class Solution {
public:
    int StrToInt(string str) {
        if(str.empty())
            return 0;
        
        long long ret = 0;
        int label = 1;
        if(str[0] == '-') label = -1;
        
        int i = 0; // 滤除空格
        while(str[i] == ' ')
            i++;
        
        for (int j = i; j < str.size(); ++j) {
            if(j == i && (str[j] == '-' || str[j] == '+'))
                continue;
            if(str[j] < '0' || str[j] > '9')
                return 0;
            
            ret = ret * 10 + (str[j] - '0');
        }
        
        // 处理越界
        if(label * ret > 0x7FFFFFFF || label * ret < (signed int)0x80000000)
            return 0;
        
        return label * ret;
    }
};
```



## 位运算

### 面试题15 二进制中1的个数（核心思想：位操作）

【[OJ](https://www.nowcoder.com/practice/8ee967e43c2c4ec193b040ea7fbb10b8?tpId=13&tqId=11164&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/25/)】输入一个32位整数，输出该数二进制表示中1的个数。负数在计算机中用其绝对值的补码来表示。

```
输入：9
输出：2
解释：9的二进制表示是1001，一共有2个1。

输入：-2
输出：31
解释：-2在计算机里会被表示成11111111111111111111111111111110，
      一共有31个1。
```

**题解**：n & -n可以得到最后一位1，每次减去最后一位1，统计减到零时所减的次数

```cpp
class Solution {
public:
    // 循环次数等于整数二进制位中1的个数
    int  NumberOf1(int n) {
        int res = 0;
        while(n) {
            n -= (n & -n);
            res++;
        }
        return res;
    }
};
```



### 面试题56.1 数组中只出现一次的两个数字（核心思想：位操作）⭐️

【[OJ](https://www.nowcoder.com/practice/e02fdb54d7524710a7d664d082bb7811?tpId=13&tqId=11193&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/69/) / [Leetcode](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof/)】一个整型数组里除了**两个数字**之外，其他的数字都**出现了两次**。请写程序找出这两个只出现一次的数字。你可以假设这两个数字一定存在。

```
输入：[1,2,3,3,4,4]
输出：[1,2]
```

**题解**：

方法1：位运算，时间复杂度O(n)，空间复杂度O(1)

> 如果除了一个数字以外，其他数字都出现了两次，那么如何找到出现一次的数字？
>

> 答案很简单：全员进行异或操作即可。考虑异或操作的性质：对于两个操作数的每一位，相同结果为 0，不同结果为 1。那么在计算过程中，成对出现的数字的所有位会两两抵消为 0，最终得到的结果就是那个出现了一次的数字。异或满足交换律。

如果我们可以把所有数字分成两组，使得：

1. 两个只出现一次的数字在不同的组中；

2. 相同的数字会被分到相同的组中。

那么对两个组分别进行异或操作，即可得到答案的两个数字。这是解决这个问题的关键。

记这两个只出现了一次的数字为 a 和 b，那么所有数字异或的结果就等于 a 和 b 异或的结果，我们记为 x。如果我们把 x 写成二进制的形式 $x_k x_{k - 1} \cdots x_2 x_1 x_0$ 其中$x_i \in \{ 0, 1 \}$ 我们考虑一下 $x_i=0$ 和 $x_i = 1$的含义是什么？它意味着如果我们把 a 和 b 写成二进制的形式，$a_i$和$b_i$的关系——$x_i=1$表示$a_i$和$b_i$不等，$x_i=1$ 表示 $a_i$ 和 $b_i$ 相等。假如我们任选一个不为 0 的 $x_i$，按照第 $i$ 位给原来的序列分组，如果该位为 0 就分到第一组，否则就分到第二组，这样就能满足以上两个条件：

- 首先，两个相同的数字的对应位都是相同的，所以一个被分到了某一组，另一个必然被分到这一组，所以满足了条件 2。
- 这个方法在$x_i=1$的时候a和b不被分在同一组，因为$x_i=1$表示$a_i$和$b_i$不相等根据这个方法的定义「如果该位为 0 就分到第一组，否则就分到第二组」可以知道它们被分进了两组，所以满足了条件 1。

在实际操作的过程中，我们拿到序列的异或和 x 之后，对于这个「位」是可以任取的，只要它满足 $x_i = 1$。

**算法**：

- 先对所有数字进行一次异或，得到两个出现一次的数字的异或值 x^y。

- 在异或结果x^y中找到任意为 1 的位。

- 根据这一位对所有的数字进行分组，第k位为1的集合和第k位不是1的集合，其中x y分别在这两个集合，且相同的元素是在同一个集合里面。

- 在每个组内进行异或操作，得到两个数字x、y，即转化为求重复数字中的单个数值的问题。

```cpp
class Solution {
public:
    vector<int> findNumsAppearOnce(vector<int>& nums) {
        int sum = 0; // x^y
        for(int num : nums) sum ^= num; // 求x^y
        int k = 0;
        while(!(sum >> k & 1)) k++; // 找到x^y左侧第一个1
        int x = 0;
        for(int num : nums) // 求第k为为1的分组的异或结果，得到x
            if(num >> k & 1)
                x ^= num;
        return {x, sum ^ x}; // y = (x^y)^x
    }
};
```



方法2：使用哈希表，时间复杂度O(n)，空间复杂度O(n)

```cpp
class Solution {
public:
    vector<int> findNumsAppearOnce(vector<int>& nums) {
        unordered_map<int, int> hash;
        for(auto num : nums) ++hash[num];
        
        vector<int> res;
        for(auto cnt : hash) 
            if(cnt.second == 1) res.push_back(cnt.first);
            
        return res;
    }
};
```



### 面试题56.2 数组中唯一只出现一次的数字（核心思想：位运算）

【[AcWing](https://www.acwing.com/problem/content/70/)】在一个数组中除了一个数字只出现**一次**之外，其他数字都出现了**三次**。请找出那个只出现一次的数字。你可以假设满足条件的数字一定存在。

```
输入：[1,1,1,2,2,2,3,4,4,4]
输出：3
```

**题解**：

方法1：

统计所有数中第 i 位为1的个数，i为32位中的某一位，各位上1的个数除以3的余数即为落单数字对应二进制位上的数

```cpp
class Solution {
public:
    int findNumberAppearingOnce(vector<int>& nums) {
        int ans = 0;
        for (int i = 31; i >= 0; --i) {
            int cnt = 0;
            for (int x: nums) // 统计所有数中第i位为1的个数
                if (x >> i & 1) cnt ++;

            if (cnt % 3 == 1) // 除以3的余数为那个落单数字对应二进制位上的数
                ans = (ans * 2) + 1;
            else
                ans = ans * 2;
        }
        return ans;
    }
};
```



方法2：

技巧性方法

```cpp
class Solution {
public:
    int findNumberAppearingOnce(vector<int>& nums) {
        int ones = 0, twos = 0;
        for (auto x : nums) {
            ones = (ones ^ x) & ~twos;
            twos = (twos ^ x) & ~ones;
        }
        return ones;
    }
};
```



## 数学

### 面试题43  1~n整数中1出现的次数（困难）❌

【[OJ](https://www.nowcoder.com/practice/bd7f978302044eee894445e244c7eee6?tpId=13&tqId=11184&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/51/)】输入一个整数n，求从1到n这n个整数的十进制表示中1出现的次数。

例如输入12，从1到12这些整数中包含“1”的数字有1，10，11和12，其中“1”一共出现了5次。

[Leetcode : 233. Number of Digit One](https://leetcode.com/problems/number-of-digit-one/discuss/64381/4+-lines-O(log-n)-C++JavaPython)

```
输入： 12
输出： 5
```

**题解**：

```cpp
class Solution {
public:
    int NumberOf1Between1AndN_Solution(int n)
    {
        int ones = 0;
        for (long long m = 1; m <= n; m *= 10) {
            int a = n/m, b = n%m;
            ones += (a + 8) / 10 * m + (a % 10 == 1) * (b + 1);
        }
        return ones;
    }
};
```



### 面试题49 丑数

【[OJ](https://www.nowcoder.com/practice/6aa9e04fc3794f68acf8778237ba065b?tpId=13&tqId=11186&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/58/)】把只包含质因子2、3和5的数称作丑数（Ugly Number）。例如6、8都是丑数，但14不是，因为它包含质因子7。 习惯上我们把1当做是第一个丑数。求按从小到大的顺序的第N个丑数。

```
输入：5
输出：5
```

**题解**：

所谓一个数 m 是另 一个数 n 的 因子，是指 n 能被 m 整除 也就是 n%m =0 。根据丑数的定义，丑数只能被 2 、3 和 5 整除 。也就是说，如果 一 个数能被 2 整除，就连续除以 2；如果能被 3 整除，就连续除以3；如果能被 5整除，就除以连续 5。如果最后得到的是1，那么这个数就是丑数，否则不是 。

假设数组中已经有若下个排好序的且数，并且把已有最大的丑数记作M，下一个丑数肯定是前面某一个丑数乘以2、3或者5的结果。对于乘以2而言，肯定存在某一个丑数T2，排在它之前的每个丑数乘以2得到的结果都会小于已有最大的丑数，在它之后的每个丑数乘以2得到的结果都会太大。我们只需记下这个丑数的位置，同时每次生成新的丑数的时候去更新这个T2即可。对千乘以3和5而言，也存在同样的T3和T5。

```cpp
class Solution {
public:
    int getUglyNumber(int n) {
        if(n <= 6) return n;
        vector<int> q(1, 1); // 第一个丑数为1
        int i = 0, j = 0, k = 0;
        while(--n) { // 计算n-1次
            int t = min(q[i] * 2, min(q[j] * 3, q[k] * 5));
            q.push_back(t); // 保存下一个可能的丑数中最小的
            // 在已得到的丑数基础上查找下一个丑数
            if(q[i] * 2 == t) i++;
            if(q[j] * 3 == t) j++;
            if(q[k] * 5 == t) k++;
        }
        return q.back();
    }
};
```



## 递归

### 面试题10.1 斐波那契数列

【[OJ](https://www.nowcoder.com/practice/c6c7742f5ba7442aada113136ddea0c3?tpId=13&tqId=11160&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】输入一个整数n，请你输出斐波那契数列的第n项（从0开始，第0项为0，第1项是1）

**题解**：

写法1：计算n次，使用while循环，不需考虑边界，返回pre2

```cpp
class Solution {
public:
    int Fibonacci(int n) {
        int pre2 = 0, pre1 = 1;
        while(n--) {
            int cur = pre1 + pre2;
            pre2 = pre1;
            pre1 = cur;
        }
        return pre2;
    }
};
```



写法2：计算n-1次，使用for循环，需要考虑边界，返回pre1

```cpp
class Solution {
public:
    int Fibonacci(int n) {
        if (n <= 1) return n;
        int pre2 = 0, pre1 = 1;
        for(int i = 2; i <= n; i++) {
            int cur = pre1 + pre2; 
            pre2 = pre1; // 注意赋值顺序
            pre1 = cur;
        }
        return pre1;
    }
};
```



### 面试题10.2 青蛙跳台阶

【[OJ](https://www.nowcoder.com/practice/8c82a5b80378478f9484d87d1c5f12a4?tpId=13&tqId=11161&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）

写法1：while循环，计算n-1次

```cpp
class Solution {
public:
    int jumpFloor(int number) {
        int pre2 = 1, pre1 = 2;
        while(number-- > 1) {
            int cur = pre1 + pre2;
            pre2 = pre1;
            pre1 = cur;
        }
        return pre2;
    }
};
```



写法2：for循环，计算n-2次

```cpp
class Solution {
public:
    int jumpFloor(int number) {
        if(number <= 2) return number;
        
        int pre2 = 1, pre1 = 2;
        for(int i = 3; i <= number; ++i) {
            int cur = pre1 + pre2;
            pre2 = pre1;
            pre1 = cur;
        }
        return pre1;
    }
};
```



### 面试题10 扩展  变态跳台阶

【[OJ](https://www.nowcoder.com/practice/22243d016f6b47f2a6928b4313c85387?tpId=13&tqId=11162&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

```cpp
class Solution {
public:
    int jumpFloorII(int number) {
        if(number == 0) return 0;
        
        int total = 1;
        for(int i=1; i < number; i++) // 2^(n-1)
            total*=2;
        return total;
    }
};
```



### 面试题10 相关题目  矩形覆盖

【[OJ](https://www.nowcoder.com/practice/72a5a919508a4251859fb2cfb987a0e6?tpId=13&tqId=11163&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】我们可以用2\*1的小矩形横着或者竖着去覆盖更大的矩形。请问用n个2\*1的小矩形无重叠地覆盖一个2*n的大矩形，总共有多少种方法？

**题解**：和跳台阶是一样的

```cpp
class Solution {
public:
    int rectCover(int number) {
        int pre2 = 1, pre1 = 2;
        while(number-- > 1) {
            int cur = pre1 + pre2;
            pre2 = pre1;
            pre1 = cur;
        }
        return pre2;
    }
};
```



```cpp
class Solution {
public:
    int rectCover(int number) {
        if(number <= 2) return number;
        
        int pre2 = 1, pre1 = 2;
        for(int i = 3; i <= number; ++i) {
            int cur = pre1 + pre2;
            pre2 = pre1;
            pre1 = cur;
        }
        return pre1;
    }
};
```



### 面试题16 数值的整数次方

【[OJ](https://www.nowcoder.com/practice/1a834e5e3e1a4b7ba251417554e07c00?tpId=13&tqId=11165&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

```cpp
class Solution {
public:
    double Power(double base, int exponent) {
        g_InvalidInput = false;
        
        if(equal(base, 0.0) && exponent < 0) { // 底数为0，幂小于0，非法
            g_InvalidInput = true;
        }
        
        unsigned int absExponent = (unsigned int) (exponent);
        if(exponent < 0) { // 转换为正数
            absExponent = (unsigned int) (-exponent);
        }
        
        double result = powerWithUnsignedExponent(base, absExponent);
        if(exponent < 0) { // 幂小于0，取倒数
            result = 1.0 / result;
        }
        
        return result;
    }
    
    double powerWithUnsignedExponent(double base, unsigned int exponent) {
        // 递归边界
        if(exponent == 0) return 1; // 0次幂为1
        if(exponent == 1) return base; // 1次幂为本身
        
        double result = powerWithUnsignedExponent(base, exponent >> 1);
        result *= result;
        
        if(exponent & 0x1 == 1) { // 奇数需多乘一次base
            result *= base;
        }
        
        return result;
    }
    
    bool equal(double num1, double num2) { // 判断浮点数相等
        if((num1 - num2 > -0.0000001) && (num1 - num2 < 0.0000001)) {
            return true;
        } else return false;
    }
    
    bool g_InvalidInput;
};
```



## 回溯

### 面试题12 矩阵中的路径（核心思想：DFS&回溯）

【[OJ](https://www.nowcoder.com/practice/c61c6999eecb4b8f88a98f66b273a3cc?tpId=13&tqId=11218&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/21/)】请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中的任意一个格子开始，每一步可以在矩阵中向左，向右，向上，向下移动一个格子。如果一条路径经过了矩阵中的某一个格子，则之后不能再次进入这个格子。

```
matrix=
[
  ["A","B","C","E"],
  ["S","F","C","S"],
  ["A","D","E","E"]
]

str="BCCE" , return "true" 

str="ASAE" , return "false"
```

题解：

先枚举单词的起点，然后依次枚举单词的每个字母。过程中需要将已经使用过的字母改成一个特殊字母，以避免重复使用字符。

```cpp
// AcWing版答案
class Solution {
public:
    bool hasPath(vector<vector<char>>& matrix, string &str) {
        for(int i = 0; i < matrix.size(); i++) {
            for(int j = 0; j < matrix[0].size(); j++) {
                if(dfs(matrix, str, i, j, 0)) return true;
            }
        }
        return false;
    }
    
    bool dfs(vector<vector<char>>& matrix, string &str, int x, int y, int u) {
        if(matrix[x][y] != str[u]) return false; // 必须先判断当前字符是否匹配
        if(u == str.size() - 1) return true; // 判断是否匹配所有字符
        
        char tmp = matrix[x][y];
        matrix[x][y] = '*';
        int dx[] = {-1,0,1,0}, dy[] = {0,1,0,-1};
        for(int i = 0; i < 4; i++) {
            int a = x + dx[i], b = y + dy[i];
            if(a < 0 || a >= matrix.size() || b < 0 || b >= matrix[0].size()) continue;
            if(matrix[a][b] == '*') continue; // 不可重复进入相同的格子
            if(dfs(matrix, str, a, b, u + 1)) return true;
        }
        matrix[x][y] = tmp;
        return false;
    }
};

// 牛客网版答案
class Solution {
public:
    bool hasPath(char* matrix, int rows, int cols, char* str)
    {
        for(int i = 0; i < rows; i++) {
            for(int j = 0; j < cols; j++) {
                if(dfs(matrix, rows, cols, str, i, j, 0)) return true;
            }
        }
        return false;
    }

    bool dfs(char* matrix, int rows, int cols, char* str, int x, int y, int u) {
        if(matrix[cols*x + y] != str[u]) return false; // 必须先判断当前字符是否匹配
        if(u == strlen(str) - 1) return true; // 判断是否匹配所有字符
        
        char tmp = matrix[cols*x + y];
        matrix[cols*x + y] = '*';
        int dx[] = {-1,0,1,0}, dy[] = {0,1,0,-1};
        for(int i = 0; i < 4; i++) {
            int a = x + dx[i], b = y + dy[i];
            if(a < 0 || a >= rows || b < 0 || b >= cols) continue;
            if(matrix[cols*a + b] == '*') continue; // 不可重复进入相同的格子
            if(dfs(matrix, rows, cols, str, a, b, u + 1)) return true;
        }
        matrix[cols*x + y] = tmp;
        return false;
    }
};
```



## 搜索

### 面试题13 机器人的运动范围（核心思想：DFS/BFS）⭐️⭐️

【[OJ](https://www.nowcoder.com/practice/6e5207314b5241fb83f2329e89fdecc8?tpId=13&tqId=11219&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】地上有一个m行和n列的方格。一个机器人从坐标0,0的格子开始移动，每一次只能向左，右，上，下四个方向移动一格，但是不能进入行坐标和列坐标的数位之和大于k的格子。 例如，当k为18时，机器人能够进入方格（35,37），因为3+5+3+7 = 18。但是，它不能进入方格（35,38），因为3+5+3+8 = 19。请问该机器人能够达到多少个格子？

**题解**：

方法1：DFS，需要注意对起点的处理

写法1：通过变量记录路径长度

```cpp
class Solution {
public:
    int movingCount(int threshold, int rows, int cols)
    {
        if(rows <= 0 || cols <= 0 || threshold < 0) return 0;
        vector<vector<bool>> memo(rows, vector<bool>(cols, false));
        // memo[0][0] = 1; // 标记起点已走过，若dfs函数一开始没写备忘，则此处不可少
        int res = 1; // 注意，起点也算长度
        dfs(0, 0, rows, cols, threshold, res, memo);
        return res;
    }
    
    void dfs(int x, int y, int rows, int cols, int k, int& res, vector<vector<bool>>& memo) {
        memo[x][y] = true; // 这里添加备忘，起点便不需特殊处理
        int dx[] = {-1,0,1,0}, dy[] = {0,1,0,-1};
        for(int i = 0; i < 4; i++) {
            int a = x + dx[i], b = y + dy[i];
            if(a < 0 || a >= rows || b < 0 || b >= cols) continue;
            if(memo[a][b]) continue;
            if(sum(a) + sum(b) > k) continue;
            
            // memo[a][b] = true; // 这里添加备忘，则在注意函数需要先备忘起点
            res++;
            dfs(a, b, rows, cols, k, res, memo);
        }
    }
    
    int sum(int a) {
        int s = 0;
        while (a) {
            s += a % 10;
            a /= 10;
        }
        return s;
    }
};
```



写法2：通过dfs函数返回路径长度

```cpp
class Solution {
public:
    int movingCount(int threshold, int rows, int cols)
    {
        if(rows <= 0 || cols <= 0 || threshold < 0) return 0;
        vector<vector<bool>> memo(rows, vector<bool>(cols, false));
        // memo[0][0] = 1; // 标记起点已走过，若dfs函数一开始没写备忘，则此处不可少
        return dfs(0, 0, rows, cols, threshold, memo);
    }
    
    int dfs(int x, int y, int rows, int cols, int k, vector<vector<bool>>& memo) {
        int cnt = 1;
        memo[x][y] = true; // 这里添加备忘，起点便不需特殊处理
        int dx[] = {-1,0,1,0}, dy[] = {0,1,0,-1};
        for(int i = 0; i < 4; i++) {
            int a = x + dx[i], b = y + dy[i];
            if(a < 0 || a >= rows || b < 0 || b >= cols) continue;
            if(memo[a][b]) continue;
            if(sum(a) + sum(b) > k) continue;
            
            // memo[a][b] = true; // 这里添加备忘，则在注意函数需要先备忘起点
            cnt += dfs(a, b, rows, cols, k, memo);
        }
        return cnt;
    }
    
    int sum(int a) {
        int s = 0;
        while (a) {
            s += a % 10;
            a /= 10;
        }
        return s;
    }
};
```



方法2：BFS

设置变量res记录路径点个数，若在取出时记录路径点个数则初始化为0，在添加时记录则初始化为1

```cpp
class Solution {
public:
    int movingCount(int threshold, int rows, int cols)
    {
        if(rows <= 0 || cols <= 0 || threshold < 0) return 0;
        vector<vector<bool>> memo(rows, vector<bool>(cols, false));
        queue<pair<int, int>> qu;
        int res = 0; // 若在取出时记录路径点个数则初始化为0，在添加时记录则初始化为1
        
        qu.push({0, 0});
        memo[0][0] = true;
        while(!qu.empty()) {
            auto t = qu.front(); qu.pop();
            res++; // 在取出时记录路径点个数
            int dx[] = {-1,0,1,0}, dy[] = {0,1,0,-1};
            for(int i = 0; i < 4; i++) {
                int a = t.first + dx[i], b = t.second + dy[i];
                if(a < 0 || a >= rows || b < 0 || b >= cols) continue;
                if(memo[a][b]) continue;
                if(sum(a) + sum(b) > threshold) continue;
                
                memo[a][b] = true; // 这里添加备忘，则在注意函数需要先备忘起点
                // res++; // 在添加时记录路径点个数
                qu.push({a, b});
            }
        }
        return res;
        
    }
    
    int sum(int a) {
        int s = 0;
        while (a) {
            s += a % 10;
            a /= 10;
        }
        return s;
    }
};
```



## 动态规划和贪心

### 面试题14 剪绳子（核心思想：贪心&DP）

【[OJ](https://www.nowcoder.com/practice/57d85990ba5b440ab888fc72b0751bf8?tpId=13&tqId=33257&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/description/24/)】给你一根长度为n的绳子，请把绳子剪成整数长的m段（m、n都是整数，n>1并且m>1，m<=n），每段绳子的长度记为k[1],...,k[m]。请问k[1]x...xk[m]可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

```
输入：8
输出：18
```

**题解：**

方法1：贪心，时间复杂度$O(n)$

首先把一个正整数 $N$ 拆分成若干正整数只有有限种拆法，所以存在最大乘积。假设 $N = n_1 + n_2 + ... + n_k$，并且 $n_1 \times n_2 \times ... \times n_k$ 是最大乘积。

1. 显然1不会出现在其中；
2. 如果对于某个 $i$ 有 $n_i \ge 5$，那么把 $n_i$ 拆分成 $3 + (n_i - 3)$，我们有 $3(n_i - 3) = 3n_i - 9 \gt n_i$；
3. 如果 $n_i = 4$，拆成 $2 + 2$乘积不变，所以不妨假设没有4；
4. 如果有三个以上的2，那么 $3 \times 3 \gt 2 \times 2 \times 2$，所以替换成3乘积更大；

综上，选用尽量多的3，直到剩下2或者4时，用2。
时间复杂度分析：当 $n$ 比较大时，$n$ 会被拆分成 $\lceil n / 3 \rceil$ 个数，我们需要计算这么多次减法和乘法，所以时间复杂度是 $O(n)$。

```c
class Solution {
public:
    int cutRope(int n) {
        if (n <= 3) return 1 * (n - 1); // 因为至少拆成两段，小于3则拆出1和n-1
        int res = 1;
        if (n % 3 == 1) res = 4, n -= 4; // 余1则拆出两个2
        else if (n % 3 == 2) res = 2, n -= 2; // 余2则拆出一个2
        while(n) res *= 3, n -= 3; // 可被3整除，将n拆成3的和
        return res;
    }
};
```



方法2：DP，时间复杂度$O(n^2)$

状态定义：dp[i] 表示表示把长度为 i 的绳子剪成若干段之后各段长度乘积的最大值。

状态转移方程：dp[i] = max(dp[i], dp[j] * dp[i-j])，其中 0<i<n。若不剪长度为 i 的绳子则最大值为 dp[i]，若将长度为 i 的绳子剪成长度为 j 和长度为 i-j 的两段，则最大值为dp[j] * dp[i-j]。长度为 i 时的最大值为这两种情况中的最大值。

初始值：当长度小于3则必须拆出1和length-1两段，当长度大于等于4时，为了计算出dp数组后面的值，需要根据状态定义得到 dp[1],dp[2],dp[3] 的值，为了套用状态转移方程以求得dp[3]之后的值，dp[1],dp[2],dp[3] 值应初始化为不剪绳子时的乘积，即dp[1] = 1, dp[2] = 2, dp[3] = 3; 这个地方比较难理解。

```cpp
class Solution {
public:
    int cutRope(int n) {
        if (n <= 3) return 1 * (n - 1); // 因为至少拆成两段，小于3则拆出1和n-1
        vector<int> dp(n + 1, 0);
        dp[1] = 1, dp[2] = 2, dp[3] = 3;
        for (int i = 4; i <= n; i++) {
            for (int j = 1; j <= i / 2; j++) {
                dp[i] = max(dp[i], dp[j] * dp[i - j]);
            }
        }
        return dp[n];
    }
};
```



### 面试题46 把数字翻译成字符串（核心思想：DP）⭐️⭐️

【[AcWing](https://www.acwing.com/problem/content/55/)】给定一个数字，我们按照如下规则把它翻译为字符串：0翻译成”a”，1翻译成”b”，……，11翻译成”l”，……，25翻译成”z”。

一个数字可能有多个翻译。例如12258有5种不同的翻译，它们分别是”bccfi”、”bwfi”、”bczi”、”mcfi”和”mzi”。请编程实现一个函数用来计算一个数字有多少种不同的翻译方法。

```
输入："12258"
输出：5
```



**题解**：动规，dp[i]表示前 i 位数字有多少种翻译方法，分两种情况：

- 第 i 位数字作为一组进行翻译，即第 i 位数字不可动，那么翻译方法数为dp[i-1]， 即前 i-1 个数字的翻译方法数
- 第 i 位和第 i-1 位数字作为一组进行翻译，即第 i 和第 i - 1这两位数字均不可动，那么翻译方法数为dp[i-2]。但第 i-1 和第 i-2 位数字组成的数字需在0\~25之间并且不能以0开头（按题意，01不等于1），即这两位数字在10\~25之间

状态转移方程：

当i和i-1两位数字在10~25之间：`dp[i] = dp[i-1] + dp[i-2]`

否则：`dp[i] = dp[i-1]`

边界：dp[0] = 1 即 没有数字时，翻译方法数为1，为了使得dp[1] = dp[0] = 1。或者将dp数组全部初始化为1

```cpp
class Solution {
public:
    int getTranslationCount(string s) {
        int n = s.size();
        vector<int> dp(n + 1, 0); // 也可全部初始化为1，省略后面的dp[0] = 1
        
        dp[0] = 1;
        for(int i = 1; i <= n; i++) {
            dp[i] = dp[i-1];
            if(i >= 2) { // 确保s[i-2]合法
                int t = (s[i-2] - '0') * 10 + s[i-1] - '0'; // i从1开始，第i个数字为s[i-1]
                if(t >= 10 && t <= 25) dp[i] += dp[i-2];
            }
        }
        return dp[n];
    }
};
```



### 面试题47 礼物的最大价值（核心思想：DP）⭐️⭐️

【[AcWing](https://www.acwing.com/problem/content/56/)】在一个m×n的棋盘的每一格都放有一个礼物，每个礼物都有一定的价值（价值大于0）。你可以从棋盘的左上角开始拿格子里的礼物，并每次向右或者向下移动一格直到到达棋盘的右下角。给定一个棋盘及其上面的礼物，请计算你最多能拿到多少价值的礼物？

```
输入：
[
  [2,3,1],
  [1,7,1],
  [4,6,1]
]
输出：19
解释：沿着路径 2→3→7→6→1 可以得到拿到最大价值礼物。
```



**题解**：动规，状态转移方程：`dp[i][j] = max(dp[i-1][j], dp[i][j-1]) + grid[i-1][j-1]`

此题用dfs做的话时间复杂度为指数级的，而动规的时间复杂度为O(m*n)

```cpp
class Solution {
public:
    int getMaxValue(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
        for(int i = 1; i <= m; i++) { // 为便于处理i-1,j-1从i,j从1开始
            for(int j = 1; j <= n; j++) {
                dp[i][j] = max(dp[i-1][j], dp[i][j-1]) + grid[i-1][j-1]; // dp[i][j]对应的格子值为grid[i-1][j-1]
            }
        }
        return dp[m][n];
    }
};
```



### 面试题60 n个骰子的点数（核心思想：DP/DFS）

【[AcWing](https://www.acwing.com/problem/content/76/)】将一个骰子投掷n次，获得的总点数为s，s的可能范围为n\~6n。掷出某一点数，可能有多种掷法，例如投掷2次，掷出3点，共有[1,2],[2,1]两种掷法。请求出投掷n次，掷出n~6n点分别有多少种掷法。

```
输入：n=1
输出：[1, 1, 1, 1, 1, 1]
解释：投掷1次，可能出现的点数为1-6，共计6种。每种点数都只有1种掷法。所以输出[1, 1, 1, 1, 1, 1]。

输入：n=2
输出：[1, 2, 3, 4, 5, 6, 5, 4, 3, 2, 1]
解释：投掷2次，可能出现的点数为2-12，共计11种。每种点数可能掷法数目分别为1,2,3,4,5,6,5,4,3,2,1。
     所以输出[1, 2, 3, 4, 5, 6, 5, 4, 3, 2, 1]。
```

**题解**：

方法1：DP，时间复杂度O(n^2^)

状态表示：`dp[i][j]`表示前 i 个骰子扔出和为 j 的方案数

状态计算：根据最后一次的点数，划分为6个集合，因为第i个骰子可能扔出1-6的点数，则`dp[i][j]=dp[i-1][j-1]+dp[i-1][j-2]+dp[i-1][j-3]+dp[i-1][j-4]+dp[i-1][j-5]+dp[i-1][j-6]`。最后一次的点数为 k 时的方案数为`dp[i-1][j-k]`，因而状态转移方程为`dp[i][j] = dp[i-1][j-1]+ ... + dp[i-1][j-6]` 

边界条件： 用一个骰子扔出和为0-6的可能数为1，即`dp[1][j] = 1 | j=[1,6]`

```cpp
class Solution {
public:
    vector<int> numberOfDice(int n) {
        vector<vector<int>> dp(n + 1, vector<int>(6 * n + 1, 0)); // dp[i][j]表示前i个骰子扔出和为j的方案数
        for(int j = 1; j <= 6; j++) // 用一个骰子扔出和为0-6的可能数为1
            dp[1][j] = 1;
        
        for (int i = 2; i <= n; i++) // i从2开始
            for (int j = 1; j <= i * 6; j++)
                for (int k = 1; k <= 6; k++)
                    if (j >= k) dp[i][j] += dp[i - 1][j - k];
                        
        return vector<int>(dp[n].begin() + n, dp[n].end());
    }
};

```

方法2：DFS

指数级时间复杂度

dfs(n, sum) 表示前 n 个骰子点数和为 sum 时的方案数

```cpp
class Solution {
public:
    vector<int> numberOfDice(int n) {
        vector<int>res;
        for(int i = n; i <= n * 6; i ++) res.push_back(dfs(n,i));
        return res;
    }
    int dfs(int n, int sum){
        if(sum < 0) return 0; // 和不可能小于0
        if(n == 0) return !sum; // n=0时，sum不为0则为一合法方案，返回1
        int res;
        for(int i = 1; i <= 6; ++i){
            res += dfs(n-1, sum-i)
        }
        return res;
    }
};
```



### 面试题63 股票的最大利润（核心思想：贪心）

【[AcWing](https://www.acwing.com/problem/content/79/)】假设把某股票的价格按照时间先后顺序存储在数组中，请问买卖 **一次** 该股票可能获得的利润是多少？

例如一只股票在某些时间节点的价格为[9, 11, 8, 5, 7, 12, 16, 14]。如果我们能在价格为5的时候买入并在价格为16时卖出，则能收获最大的利润11。

```
输入：[9, 11, 8, 5, 7, 12, 16, 14]
输出：11
```

**题解**：贪心思想，时间复杂度O(n)

由于只允许做一次股票买卖交易，枚举每一天作为卖出的日子，买入日子一定在卖出日子之前，为了获利最多，希望买入的日子的股票价格尽可能低。暴力做法是O(n^2^)，卖出时遍历前面 i 天的价格，找出最小值。可以维护一个最小值变量 min，存储前 i 天价格最小值，从而省去第二次遍历，时间复杂度降低到O(n)。

```cpp
class Solution {
public:
    int maxDiff(vector<int>& nums) {
        if(nums.empty()) return 0;
        
        int res = 0;
        for(int i = 1, minv = nums[0]; i < nums.size(); i++) { // 遍历数组，j为最低购入价格
            minv = min(minv, nums[i]); // 记录第0-i天最小的购入价格
            if(minv < nums[i])
                res = max(res, nums[i] - minv); // 记录0-i天最大的利润
        }
        return res;
    }
};
```



## 发散思维、抽象建模

### 面试题61 扑克牌中的顺子

【[OJ](https://www.nowcoder.com/practice/762836f4d43d43ca9deb273b3de8e1f4?tpId=13&tqId=11198&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/77/)】从扑克牌中随机抽5张牌，判断是不是一个顺子，即这5张牌是不是连续的。2～10为数字本身，A为1，J为11，Q为12，K为13，大小王可以看做任意数字。为了方便，大小王均以0来表示，并且假设这副牌中大小王均有两张。

```
输入：[8,9,10,11,12]
输出：true

输入：[0,8,9,11,12]
输出：true
```

**题解**：

先排序，再判断除0以外是否有重复，有重复则不可能是顺子，最后判断除0外最大数与组小数的差是否小于等于4，等于4时不需要用0补，小于4时需要用0补，大于4则不可能是顺子。

```cpp
class Solution {
public:
    bool isContinuous( vector<int> nums ) {
        if(nums.empty()) return false;
        sort(nums.begin(), nums.end()); // 先排序
        for(int i = 1; i < nums.size(); i++) { // 判断除0以外的数是否有重复
            if(nums[i] && nums[i] == nums[i-1])
                return false; // 有重复则不可能是顺子
        }
        
        for(auto x :nums) { // 判断除0以外最小的数与最大的数的差值是否小于等于4
            if(x) return nums.back() - x <= 4; // 差值小于等于4则肯定是顺子
        }
    }
};
```



### 面试题64 求1+2+3+...+n

【[OJ](https://www.nowcoder.com/practice/7a0da8fc483247ff8800059e12d7caf1?tpId=13&tqId=11200&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWing](https://www.acwing.com/problem/content/description/80/)】求1+2+…+n,要求不能使用乘除法、for、while、if、else、switch、case等关键字及条件判断语句（A?B:C）。

```
输入：10
输出：55
```

**题解**：

使用递归解法最重要的是指定返回条件，但是本题无法直接使用 if 语句来指定返回条件。

求解方法：

1. 利用构造函数

2. 利用虚函数

3. 利用函数指针

4. 利用模板类型

5. 利用条件与&&的短路原则

   条件与 && 具有短路原则，即在第一个条件语句为 false 的情况下不会去执行第二个条件语句。利用这一特性，将递归的返回条件取非然后作为 && 的第一个条件语句，递归的主体转换为第二个条件语句，那么当递归的返回条件为 true 的情况下就不会执行递归的主体部分，递归返回。

   本题的递归返回条件为 n <= 0，取非后就是 n > 0；递归的主体部分为 sum += Sum_Solution(n - 1)，转换为条件语句后就是 (sum += Sum_Solution(n - 1)) > 0。

```cpp
class Solution {
public:
    int Sum_Solution(int n) {
        int sum = n;
        // if(!sum) return 0; // 到0时返回
        sum && (sum += Sum_Solution(n - 1)); // sum = 0 时，后半句不执行，即返回0
        return sum;
    }
};
```



### 面试题65 不用加减乘除做加法

【[OJ](https://www.nowcoder.com/practice/59ac416b4b944300b617d4f7f111b215?tpId=13&tqId=11201&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [AcWIng](https://www.acwing.com/problem/content/description/81/)】写一个函数，求两个整数之和，要求在函数体内不得使用＋、－、×、÷ 四则运算符号。

```
输入：num1 = 1 , num2 = 2
输出：3
```

**题解**：

首先看十进制是如何做的： 5+7=12，三步走 

第一步：相加各位的值，不算进位，得到2。 

第二步：计算进位值，得到10. 如果这一步的进位值为0，那么第一步得到的值就是最终结果。 

第三步：重复上述两步，只是相加的值变成上述两步的得到的结果2和10，得到12。 

同样我们可以用三步走的方式计算二进制值相加： 5-101，7-111 

第一步：相加各位的值，不算进位，得到010，二进制每位相加就相当于各位做异或操作，101^111。

第二步：计算进位值，得到1010，相当于各位做与操作得到101，再向左移一位得到1010，(101&111)<<1。 

第三步重复上述两步， 各位相加 010^1010=1000，进位值为100=(010&1010)<<1。     继续重复上述两步：1000^100 = 1100，进位值为0，跳出循环，1100为最终结果。

```cpp
class Solution {
public:
    int Add(int num1, int num2)
    {
        while (num2) { // 进位为0时退出循环
            int sum = num1 ^ num2; // 计算不进位时的和，不进位时对应的两位一定不同
            int carry = (num1 & num2) << 1; // 计算进位， 对应的两位都为1才会进位，因而使用&运算
            num1 = sum;
            num2 = carry; // 进位结束时num2为0
        }
        return num1;
    }
};
```

