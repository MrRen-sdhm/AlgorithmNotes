## 剑指offer题解 C++版 ⚔️ ✏️⭐️

#### 参考资料

[**剑指offer题解-CyC2018**](https://cyc2018.github.io/CS-Notes/#/notes/%E5%89%91%E6%8C%87%20Offer%20%E9%A2%98%E8%A7%A3%20-%20%E7%9B%AE%E5%BD%951)  [**剑指Offer系列刷题笔记汇总**](https://blog.csdn.net/c406495762/article/details/79247243)



#### 数组

##### 面试题3 数组中重复的数字⭐️

【[OJ](https://www.nowcoder.com/practice/623a5ac0ea5b4e5f95552655361ae0a8?tpId=13&tqId=11203&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】在一个长度为n的数组里的所有数字都在0到n-1的范围内。 数组中某些数字是重复的，但不知道有几个数字是重复的。也不知道每个数字重复几次。请找出数组中任意一个重复的数字。 例如，如果输入长度为7的数组{2,3,1,0,2,5,3}，那么对应的输出是第一个重复的数字2。

**题解**：要求时间复杂度 O(N)，空间复杂度 O(1)。因此不能使用排序的方法，也不能使用额外的标记数组。

对于这种数组元素在 [0, n-1] 范围内的问题，可以**将值为 i 的元素调整到第 i 个位置上**进行求解。本题要求找出重复的数字，因此在调整过程中，如果第 i 位置上已经有一个值为 i 的元素，就可以知道 i 值重复。

以 (2, 3, 1, 0, 2, 5) 为例，遍历到位置 4 时，该位置上的数为 2，但是第 2 个位置上已经有一个 2 的值了，因此可以知道 2 重复：

<img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/643b6f18-f933-4ac5-aa7a-e304dbd7fe49.gif" alt="img" style="zoom:67%;" />

```C++
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



##### 面试题4 二维数组中的查找

【[OJ](https://www.nowcoder.com/practice/abc3fe2ce8e146608e868a70efebf62e?tpId=13&tqId=11154&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

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

<img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/35a8c711-0dc0-4613-95f3-be96c6c6e104.gif" alt="img" style="zoom:67%;" />

```C++
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



##### 面试题11 旋转数组的最小数字（核心思想：二分查找）⭐️

【[OJ](https://www.nowcoder.com/practice/9f3231a991af4f55b95579b44b7a01ba?tpId=13&tqId=11159&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个**非递减排序**的数组的一个旋转，输出旋转数组的最小元素。例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

**题解**：旋转之后的数组实际上可以划分为两个排序的子数组，而且前面子数组的元素都大于或者等于后面子数组的元素。我们还注意到最小的元素刚好是这两个子数组的分界线。在排序的数组中我们可以用**二分查找**法实现**O(logn)**的查找。

- 当nums[mid] <= nums[high]) 时中间值比右侧子串最后一个元素小，说明中间值属于右侧子串，其本身可能为最小值，并且其右侧不会有更小值
- 当nums[mid] > nums[high]) 时中间值比比右侧子串最后一个元素大，说明中间值属于左侧子串，其本身不是最小值，并且其左侧不会有更小值

如果数组元素允许重复，会出现一个特殊的情况：nums[low] == nums[mid] == nums[high]，此时无法确定解在哪个区间，需要切换到顺序查找。例如对于数组 {1,1,1,0,1}，low、mid 和 high 指向的数都为 1，此时无法知道最小数字 0 在哪个区间。

```C++
// 标准二分查找的变形
class Solution {
public:
    int minNumberInRotateArray(vector<int> nums) {
        if(nums.empty()) return 0;
        
        int low = 0, high = nums.size() - 1, mid;
        
        while(low < high) {
            mid = (low + high) / 2;
            
            // 三个下标指向的元素值相等，只能进行顺序查找
            if(nums[mid] == nums[low] && nums[mid] >= nums[high]) {
                return minNumber(nums, low, high);
            }
            else if(nums[mid] <= nums[high])// 中间值比右侧子串最后一个元素小，说明中间值属于右侧子串，其本身可能为最小值，并且其右侧不会有更小值
                high = mid;
            else
                low = mid + 1;
        }
        
        return nums[low];
    }
    
    int minNumber(vector<int> nums, int l, int h) {
        for (int i = l; i < h; ++i)
            if (nums[i + 1] < nums[i]) // 比前面数小的即为最小值
                return nums[i + 1];
        return nums[l];
	}
};
    
// 剑指offer解法，不易理解循环条件
class Solution {
public:
    int minNumberInRotateArray(vector<int> nums) {
        if(nums.empty()) return 0;
        
        int low = 0, high = nums.size() - 1, mid;
        
        while(nums[low] >= nums[high]) { // 左侧子串元素应大于右侧
            if(high - low == 1) { // 找到最小值所在位置
                mid = high;
                break;
            }
            
            mid = (low + high) / 2;
            
            // 三个下标指向的元素值相等，只能进行顺序查找
            if(nums[mid] == nums[low] && nums[mid] >= nums[high]) {
                return MinOrder(nums, low, high);
            }
            
            if(nums[mid] >= nums[low]) // 中间值比左侧子串第一个元素大，说明中间值属于左侧子串，左侧不会有最小值
                low = mid;
            else if(nums[mid] <= nums[high])// 中间值比右侧子串最后一个元素小，说明中间值属于右侧子串，右侧不会有最小值
                high = mid;
        }
        
        return nums[mid];
    }
    
    int MinOrder(vector<int> nums, int low, int high) {
        int ret = nums[low];
        
        // 顺序查找最小元素
        for(int i = 0; i < nums.size(); i++) {
            if(nums[i] < ret) {
                ret = nums[i];
            }
        }
        
        return ret;
    }
};
```



##### 面试题21 调整数组顺序使奇数位于偶数前面（核心思想：双指针、partition）⭐️

【[OJ](https://www.nowcoder.com/practice/beb5aa231adc45b2a5dcc5b62c93f593?tpId=13&tqId=11166&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分，并保证奇数和奇数，偶数和偶数之间的相对位置不变。

**题解**：若相对位置可变，使用双指针分别从首尾查找偶数和奇数，并交换。时间复杂度约O(n/2)，空间复杂度O(1)

若相对位置不变，需使用辅助数组并遍历两次，先放入奇数再放入偶数。时间复杂度约O(2*n)，空间复杂度O(n)

```C++
class Solution {
public:
    // 使用双指针，不稳定
    void reOrderArray_(vector<int> &array) { // 类似快排的partition, 不稳定
        if(array.empty()) return;
        
        int pBegin = 0;
        int pEnd = array.size() - 1;
        
        while(pBegin < pEnd) {
            // 向后移动begin，直到其指向偶数
            while(pBegin < pEnd && ((array[pBegin] & 0x1) != 0)) {
                pBegin++;
            }
            
            // 向前移动end，直到其指向奇数
            while(pBegin < pEnd && ((array[pEnd] & 0x1) == 0)) {
                pEnd--;
            }
            
            if(pBegin < pEnd) { // begin指向偶数，end指向奇数，交换
                swap(array[pBegin], array[pEnd]);
            }
        }
    }
    // 使用辅助数组，稳定
    void reOrderArray(vector<int> &array) { // 准备辅助数组，依次放入奇数和偶数
        if(array.empty()) return;
        
        vector<int> result;
        int num = array.size();
        
        for(int i=0; i < num; i++) {
            if((array[i] & 0x1) != 0) // 奇数
                result.push_back(array[i]);
        }
        
        for(int i=0; i < num; i++) {
            if((array[i] & 0x1) == 0) // 偶数
                result.push_back(array[i]);
        }
        
        array = result;
    }
};
```



##### 面试题29 顺时针打印矩阵

【[OJ](https://www.nowcoder.com/practice/9b4c81a02cd34f76be2659fa0d54342a?tpId=13&tqId=11172&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下4 X 4矩阵： 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.

```C++
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



##### 面试题39 数组中出现次数超过—半的数字

【[OJ](https://www.nowcoder.com/practice/e8a1b01a2df14cb2b228b30ee6a92163?tpId=13&tqId=11181&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。

**题解**：使用 cnt 来统计一个元素出现的次数，当遍历到的元素和统计元素相等时，令 cnt++，否则令 cnt--。如果前面查找了 i 个元素，且 cnt == 0，说明前 i 个元素没有 majority，或者有 majority，但是出现的次数少于 i / 2 ，因为如果多于 i / 2 的话 cnt 就一定不会为 0 。此时剩下的 n - i 个元素中，majority 的数目依然多于 (n - i) / 2，因此继续查找就能找出 majority。

```C++
class Solution {
public:
    // 若某数出现次数超过一半，那么两两比较，至少有一次出现相邻两数相等，即使间隔排列，如12322
    int MoreThanHalfNum_Solution(vector<int> numbers) {
        int majority = numbers[0], cnt = 1;
        for (int i = 1; i < numbers.size(); i++) { // 找出可能的majoryty
            cnt = numbers[i] == majority ? cnt + 1 : cnt - 1; // 等于majoryty cnt+1 否则 -1
            if (cnt == 0) { // cnt为0时更新majoryty
                majority = numbers[i];
                cnt = 1; // cnt置1
            }
        }
        cnt = 0;
        for (int val : numbers) // 遍历数组，确定此majoryty的数量
            if (val == majority)
                cnt++;
        return cnt > numbers.size() / 2 ? majority : 0;
    }
};
```



##### 面试题40 最小的 K 个数（核心思想：partition）⭐️⭐️

【[OJ](https://www.nowcoder.com/practice/6a296eb82cf844ca8539b57c23e6e9bf?tpId=13&tqId=11182&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4,。

**题解**：

解法一：快速选择，时间复杂度O(n)，空间复杂度O(1)

使用partition将数组划分为<=pivot和>=pivot两个部分，若pivot所处位置之前有k个数则舍弃后面的数，若pivot所处位置之前不到k个数则舍弃前面的数。模板中while循环后，j 即为pivot所在位置，注意此 j 是**相对整个数组**而不是相对某个区间，因而包括pivot在内的左侧部分元素为[0, j]，长度为 j+1，若k<=j+1则第k个数在[l, j]区间内，否则在[j + 1, r]区间内

经过快速选择后，原数组中第k个数即为所求，其下标为[k - 1]

```C++
vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
        vector<int> res;
        if(input.size() == 0 || k > int(input.size()) || k <= 0)
            return res;
        
        for(int i = 1; i <= k; i++) {
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

解法二、使用红黑树(multiset)，时间复杂度为O(nlogk)，空间复杂度为O(k)，特别适合处理海量数据

```C++
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

解法三、使用堆(priority_queue)，时间复杂度为O(nlogk)，空间复杂度为O(k)，特别适合处理海量数据

应该使用大顶堆来维护最小堆，而不能直接创建一个小顶堆并设置一个大小，企图让小顶堆中的元素都是最小元素。

维护一个大小为 K 的最小堆过程如下：在添加一个元素之后，如果大顶堆的大小大于 K，那么需要将大顶堆的堆顶元素去除。

```C++
class Solution {
public:
    vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
        if(input.size() == 0 || k > int(input.size()) || k <= 0)
            return vector<int>();
        
        // 最大堆，最大的元素在队列前面
        priority_queue<int, vector<int>, less<int> > maxHeap;
        for (int num : input) {
            maxHeap.push(num);
            if (maxHeap.size() > k)
                maxHeap.pop();
        }

        vector<int> res = {};
        while(!maxHeap.empty()) {
            res.push_back(maxHeap.top());
            maxHeap.pop();
        }
        
        return res;
    }
};
```



##### 面试题42 连续子数组的最大和

【[OJ](https://www.nowcoder.com/practice/459bd355da1549fa8a49e350bf3df484?tpId=13&tqId=11183&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】{6,-3,-2,7,-15,1,2,2},连续子向量的最大和为8(从第0个开始,到第3个为止)。给一个数组，返回它的最大连续子序列的和，要求时间复杂度O(n)

**题解**：从左至右求和，若和小于0，说明前面序列的和不可能为最大值，置当前值为新的和。最终取连续序列中和最大的那一个。也可通过动态规划的思想分析此问题。

```C++
class Solution {
public:
    int FindGreatestSumOfSubArray(vector<int> array) {
        if(array.empty()) return 0;
        
        int greastestSum = 0x80000000; // 最小的负数
        int sum = 0;
        
        for(int val : array) {
            sum = sum < 0 ? val : sum + val; // sum小于0则置当前置为sum
            if(sum > greastestSum) // 保存最大的和
                greastestSum = sum;
        }
        
        return greastestSum;
    }
};
```



##### 面试题45 把数组排成最小的数（核心思想：利用字符串处理大数）

【[OJ](https://www.nowcoder.com/practice/8fecd3f8ba334add803bf2a06af1b993?tpId=13&tqId=11185&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。

**题解**：可以看成是一个排序问题，在比较两个字符串 S1 和 S2 的大小时，应该比较的是 S1+S2 和 S2+S1 的大小，如果 S1+S2 < S2+S1，那么应该把 S1 排在前面，否则应该把 S2 排在前面。

```C++
class Solution {
public:
    string PrintMinNumber(vector<int> numbers) {
        int len = numbers.size();
        if(len == 0) return "";
        
        sort(numbers.begin(), numbers.end(), cmp); // 根据转换为字符串后的和进行排序
        
        string res;
        for(int i = 0; i < len; i++){
            res += to_string(numbers[i]);
        }
        return res;
    }
    
    static bool cmp(int a, int b){ // 转换为字符串求和并比较
        string A = to_string(a) + to_string(b);
        string B = to_string(b) + to_string(a);
        return A < B;
    }
};
```



##### 面试题51 数组中的逆序对（核心思想：归并排序）⭐️⭐️

【[OJ](https://www.nowcoder.com/practice/96bd6684e04a44eb80e6a68efc0ec6c5?tpId=13&tqId=11188&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组,求出这个数组中的逆序对的总数P。并将P对1000000007取模的结果输出。 即输出P%1000000007

**题解**：

方法1：暴力求解，时间复杂度O(n^2)，空间复杂度O(1)

```C++
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

<img src="https://images2017.cnblogs.com/blog/849589/201710/849589-20171015230557043-37375010.gif" alt="img" style="zoom:67%;" />

```C++
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



##### 面试题57.1 和为S的两个数字（核心思想：双指针）⭐️

【[OJ](https://www.nowcoder.com/practice/390da4f7a00f44bea7c2f3d19491311b?tpId=13&tqId=11195&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】输入一个递增**排序**的数组和一个数字S，在数组中查找两个数，使得他们的和正好是S，如果有多对数字的和等于S，输出两个数的乘积最小的。

**题解**：可以利用数组已排序的特性，设两个指针，分别指向最小值和最大值，若和小于目标和则最小值指针右移，若和大于目标值最大值指针左移

```C++
class Solution {
public:
    vector<int> FindNumbersWithSum(vector<int> array,int sum) {
        if(array.empty())
            return vector<int>();
        
        int left = 0; // 左指针指向较小的数
        int right = array.size() - 1; // 右指针指向较大的数
        
        while(left < right) {
            long long currSum = array[left] + array[right];
            
            if(currSum == sum) {
                return vector<int> {array[left], array[right]};
            }else if(currSum > sum) { // 当前和大于目标和，取较小的数加入组合
                right--;
            } else left++; // 当前和小于目标和，取较大的数加入组合
        }
        
        return vector<int>();
    }
};

// 暴力求解
class Solution_ {
public:
    vector<int> FindNumbersWithSum(vector<int> array,int sum) {
        for(int i = 0; i < array.size(); ++i) {
            for(int j = i + 1; j < array.size(); ++j) {
                if(array[i] + array[j] == sum) {
                    return vector<int> {array[i], array[j]};
                }
            }
        }
        
        return vector<int>();
    }
};
```



##### 面试题57.2  和为S的连续正数序列（核心思想：双指针）

【[OJ](https://www.nowcoder.com/practice/c451a3fd84b64cb19485dad758a55ebe?tpId=13&tqId=11194&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】小明很喜欢数学,有一天他在做数学作业时,要求计算出9~16的和,他马上就写出了正确答案是100。但是他并不满足于此,他在想究竟有多少种连续的正数序列的和为100(至少包括两个数)。没多久,他就得到另一组连续正数和为100的序列:18,19,20,21,22。现在把问题交给你,你能不能也很快的找出所有和为S的连续正数序列? Good Luck!

**题解**：本质是在**已排序**的序列中查找和为某数的连续序列。设两个指针，起初指向1和2，两指针构成滑动窗口，若窗口中的和小于目标和，则窗口加宽（右指针右移），若窗口中的和大于目标和，则窗口缩窄（左指针右移）

```C++
class Solution {
public:
    vector<vector<int> > FindContinuousSequence(int sum) {
        vector<vector<int> > res;
        int left = 1;
        int right = 2;
        
        while(left < right) {
            int currSum = (left + right)*(right - left + 1)/2; // 求和公式(a0+an)*n/2
            if(currSum == sum) {
                vector<int> seq;
                for(int i = left; i <= right; ++i) {
                    seq.push_back(i);
                }
                res.push_back(seq);
                ++left; // 找到和为sum的序列了，缩小区间
            } else if(currSum < sum) { // 和小于目标和，增大区间
                ++right;
            } else ++left; // 和大于目标和，缩小区间
        }
        
        return res;
    }
};

// 暴力求解
class Solution_ {
public:
    vector<vector<int> > FindContinuousSequence(int sum) {
        vector<vector<int> > res;
        for(int i = 1; i <= sum/2; ++i) { // 至少有两个数字，因而单个数字不会大于sum/2
            int currSum = 0; // 当前序列和
            int nextNum = i; // 下一个加入的数字
            vector<int> seq;
            
            while(true) {
                seq.push_back(nextNum);
                currSum += nextNum; // 求当前序列和
                nextNum++; // 求下一个数字
                
                if(currSum >= sum) // 大于等于目标和，退出
                    break;
            }
            if(currSum == sum) // 求得的和等于目标和
                res.push_back(seq);
        }
        
        return res;
    }
};
```



##### 面试题53.1 数字在排序数组中出现的次数（核心思想：二分查找）⭐️

【[OJ](https://www.nowcoder.com/practice/70610bf967994b22bb1c26f9ae901fa2?tpId=13&tqId=11190&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】统计一个数字在排序数组中出现的次数。

**题解**：利用二分查找，但查找某数第一次出现和最后一次出现的位置，而不是单纯的判断某个数在不在数组中

```C++
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



##### 面试题62 圆圈中最后剩下的数字（孩子们的游戏）

【[OJ](https://www.nowcoder.com/practice/f78a359491e64a50bce2d89cff857eb6?tpId=13&tqId=11199&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking) / [力扣](https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/)】0,1,,n-1这n个数字排成一个圆圈，从数字0开始，每次从这个圆圈里删除第m个数字。求出这个圆圈里剩下的最后一个数字。

例如，0、1、2、3、4这5个数字组成一个圆圈，从数字0开始每次删除第3个数字，则删除的前4个数字依次是2、0、4、1，因此最后剩下的数字是3。

**题解**：可使用list/vector模拟，也可根据公式求解。

因为C++中list不支持迭代器+n操作，删除元素必须从头查找第n个元素，使用不便，推荐使用vector。

```C++
//法一：C++实现 list容器+其迭代器实现圆形链表 （约瑟夫环问题）
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

//法二：使用vector模拟，推荐
class Solution
{
public:
    int LastRemaining_Solution(int n, int m)
    {
        if(n < 1||m < 1)
            return -1;
         
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

//法三：找出规律, 通项为：f(n,m)={f(n-1,m)+m}%n。
class Solution
{
public:
    int LastRemaining_Solution(int n, int m)
    {
        if(n < 1||m < 1)
            return -1;
         
        int last = 0;
        for(int i = 2; i <= n; i++){
            last = (last + m) % i;
        }
        return last;
    }
};
```



##### 面试题66 构建乘积数组

【[OJ](https://www.nowcoder.com/practice/94a4d381a68b47b7a8bed86f2975db46?tpId=13&tqId=11204&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】给定一个数组A[0,1,...,n-1],请构建一个数组B[0,1,...,n-1],其中B中的元素B[i]=A[0]\*A[1]\*...\*A[i-1]\*A[i+1]\*...\*A[n-1]。不能使用除法。（注意：规定B[0] = A[1] \* A[2] \* ... \* A[n-1]，B[n-1] = A[0] \* A[1] \* ...\* A[n-2];）

**题解**：不妨定义C[i] =A[0] x A[1]x ··· xA[i- 1] , D[i]=A[i+ I] x ··· xA [n-2] xA [n-1]。C[i] 可以用自上而下的顺序计算出来，即 C[i] =C[i-1] x A [i-1] 。类似的，D[i]也可以用自下而上的顺序计算出来，即 D[i] =D[i+1] xA[i+1]。

```C++
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



#### 链表

##### 面试题6 从尾到头打印链表（核心思想：栈）

【[OJ](https://www.nowcoder.com/practice/d0267f7f55b3412ba93bd35cfa8e8035?tpId=13&tqId=11156&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】输入一个链表，按链表从尾到头的顺序返回一个ArrayList。

<img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/f5792051-d9b2-4ca4-a234-a4a2de3d5a57.png" alt="img" style="zoom: 67%;" />

**题解**：

方法1：使用递归

要逆序打印链表 1->2->3（3,2,1)，可以先逆序打印链表 2->3(3,2)，最后再打印第一个节点 1。而链表 2->3 可以看成一个新的链表，要逆序打印该链表可以继续使用求解函数，也就是在求解函数中调用自己，这就是递归函数。

```C++
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

方法2：使用头插法

头插法顾名思义是将节点插入到头部：在遍历原始链表时，将当前节点插入新链表的头部，使其成为第一个节点。

链表的操作需要维护后继关系，例如在某个节点 node1 之后插入一个节点 node2，我们可以通过修改后继关系来实现：

```C++
node3 = node1.next;
node2.next = node3;
node1.next = node2;
```

<img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/58c8e370-3bec-4c2b-bf17-c8d34345dd17.gif" alt="img" style="zoom: 67%;" />

为了能将一个节点插入头部，我们引入了一个叫头结点的辅助节点，该节点不存储值，只是为了方便进行插入操作。不要将头结点与第一个节点混起来，第一个节点是链表中第一个真正存储值的节点。

<img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/0dae7e93-cfd1-4bd3-97e8-325b032b716f-1572687622947.gif" alt="img" style="zoom: 67%;" />

```java
public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
    // 头插法构建逆序链表
    ListNode head = new ListNode(-1);
    while (listNode != null) {
        ListNode memo = listNode.next;
        listNode.next = head.next;
        head.next = listNode;
        listNode = memo;
    }
    // 构建 ArrayList
    ArrayList<Integer> ret = new ArrayList<>();
    head = head.next;
    while (head != null) {
        ret.add(head.val);
        head = head.next;
    }
    return ret;
}
```

方法3：使用栈

栈具有后进先出的特点，在遍历链表时将值按顺序放入栈中，最后出栈的顺序即为逆序。

<img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/9d1deeba-4ae1-41dc-98f4-47d85b9831bc.gif" alt="img" style="zoom: 50%;" />

```C++
class Solution {
public:
    vector<int> printListFromTailToHead(ListNode* head) {
        vector<int> arrayList;
        stack<int> arrayStack;
        ListNode* pNode = head;
        
        // 遍历链表并将值存入栈中
        while (pNode) {
            arrayStack.push(pNode->val);
            pNode = pNode->next;
        }
        
        // 获取栈顶元素到vector，出栈
        while(!arrayStack.empty()) {
            arrayList.push_back(arrayStack.top());
            arrayStack.pop();
        }
        
        return arrayList;
    }
};
```

方法4：借助vector的insert方法模拟栈（不推荐，代码简单，但是每次插入都要移动整个数组的元素）

```C++
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



##### 面试题18.1 在O(1)时间内删除链表节点（核心思想：指针操作）⭐️

**题目**：给定单向链表的头指针和一个节点指针，定义一个函数在 0(1) 时间内删除该节点

**题解**：

① 如果该待删节点不是尾节点，那么可以直接将下一个节点的值赋给该待删节点，然后令该待删节点指向下下个节点，再删除下一个节点，时间复杂度为 O(1)。

<img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/1176f9e1-3442-4808-a47a-76fbaea1b806.png" alt="img" style="zoom:67%;" />



② 否则，就需要先遍历链表，找到待删节点的前一个节点，然后让前一个节点指向 null，再删除待删节点，时间复杂度为 O(N)。

<img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/4bf8d0ba-36f0-459e-83a0-f15278a5a157.png" alt="img" style="zoom:67%;" />



综上，如果进行 N 次操作，那么大约需要操作节点的次数为 N-1+N=2N-1，其中 N-1 表示 N-1 个不是尾节点的每个节点以 O(1) 的时间复杂度操作节点的总次数，N 表示 1 个尾节点以 O(N) 的时间复杂度操作节点的总次数。(2N-1)/N ~ 2，因此该算法的平均时间复杂度为 O(1)。

```C++
class Solution {
public:
    ListNode* deleteNode(ListNode *head, ListNode *tobeDelete) {
        if (head == nullptr || tobeDelete == nullptr)
            return nullptr;
        if (tobeDelete.next != nullptr) { // 要删除的节点不是尾节点
            ListNode *next = tobeDelete->next;
            tobeDelete.val = next->val;
            tobeDelete.next = next->next;
        } else {
            if (head == tobeDelete) // 只有一个节点
                head = nullptr;
            else {
                ListNode *cur = head; // 指针指向头结点
                while (cur.next != tobeDelete) // 遍历链表，找到要删除节点
                    cur = cur->next;

                cur->next = nullptr; // 删除指向待删除节点的指针

                delete tobeDelete;
                tobeDelete = nullptr;
            }
        }
        return head;
    }
}
```



##### 面试题18.2 删除链表中重复的节点（核心思想：递归）⭐️

【[OJ](https://www.nowcoder.com/practice/fc533c45b73a41b0b44ccba763f866ef?tpId=13&tqId=11209&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】在一个排序的链表中，存在重复的结点，请删除该链表中重复的结点，重复的结点不保留，返回链表头指针。 例如，链表1->2->3->3->4->4->5 处理后为 1->2->5

**题解**：递归，分当前节点是否是重复节点两种情况

- 是重复节点则跳过与当前节点相同的所有节点，并从第一个与当前节点不同的节点开始递归
- 不是重复节点则保留当前节点，从下一节点开始递归

```C++
class Solution {
public:
    ListNode* deleteDuplication(ListNode* pHead)
    {
        if (pHead == nullptr || pHead->next == nullptr) { // 只有0个或1个结点，则返回
            return pHead;
        }
        if (pHead->val == pHead->next->val) { // 当前结点是重复结点
            ListNode *pNode = pHead->next;
            while (pNode != nullptr && pNode->val == pHead->val) {
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



##### 面试题22 链表中倒数第k个结点（核心思想：双指针）⭐️

【[OJ](https://www.nowcoder.com/practice/529d3ae5a407492994ad2a246518148a?tpId=13&tqId=11167&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】输入一个链表，输出该链表中倒数第k个结点。

**题解**：设链表的长度为 N。设置两个指针 P1 和 P2，先让 P1 移动 K 个节点，则还有 N - K 个节点可以移动。此时让 P1 和 P2 同时移动，可以知道当 P1 移动到链表结尾时，P2 移动到第 N - K 个节点处，该位置就是倒数第 K 个节点。

<img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/6b504f1f-bf76-4aab-a146-a9c7a58c2029.png" alt="img" style="zoom:67%;" />

```C++
class Solution {
public:
    ListNode* FindKthToTail(ListNode* pListHead, unsigned int k) {
        if(pListHead == nullptr || k == 0) return nullptr;
        
        ListNode *slow = pListHead; // 慢指针，先指向头结点
        ListNode *fast = pListHead; // 快指针，先指向头结点
       
        
        for(int i = 0; i < k; ++i) { // 快指针先移动K个节点
            if(fast == nullptr) return nullptr; // 未移动完K次，快指针已移动到链尾，说明链长不足k
            
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





##### 面试题23  链表中环的入口节点（核心思想：双指针）⭐️

【[OJ](https://www.nowcoder.com/practice/253d2c59ec3e4bc68da16833f79a38e4?tpId=13&tqId=11208&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】给一个链表，若其中包含环，请找出该链表的环的入口结点，否则，输出null。

**题解**：使用快慢指针，快指针一次走两步，慢指针一次走一步，如果快指针可以一直指到 nullptr，则说明没有环，如果快慢指针相遇则说明有环；如果两指针相遇的时候，将快指针重新指向链表的头结点，但是现在是一次走一步，最终快慢指针一定会相遇，相遇的地方即是入环的节点；[详解](https://cyc2018.github.io/CS-Notes/#/notes/23.%20%E9%93%BE%E8%A1%A8%E4%B8%AD%E7%8E%AF%E7%9A%84%E5%85%A5%E5%8F%A3%E7%BB%93%E7%82%B9)

<img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/bb7fc182-98c2-4860-8ea3-630e27a5f29f.png" alt="img" style="zoom:67%;" />

```C++
class Solution {
public:
    ListNode* EntryNodeOfLoop(ListNode* pHead)
    {
        ListNode *fast = pHead;
        ListNode *slow = pHead;
        while(fast != nullptr && fast->next !=nullptr){
            fast = fast->next->next; // 快指针一次走两步
            slow = slow->next; // 慢指针一次走一步
            if(fast == slow) // 两指针在环内相遇
                break;
        }
        if(fast == nullptr || fast->next == nullptr) // fast指针指到链尾说明无环
            return nullptr;
        fast = pHead; // 确定有环，快指针指向链头
        while(fast != slow){ // 两指针一次走一步，直到相遇时，即为环入口
            fast = fast->next;
            slow = slow->next;
        }
        return fast;
    }
};
```



##### 面试题24 反转链表（核心思想：指针操作/递归/迭代）

【[OJ](https://www.nowcoder.com/practice/75e878df47f24fdc9dc3e400ec6058ca?tpId=13&tqId=11168&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】输入一个链表，反转链表后，输出新链表的表头。

**题解**：

方法1：指针操作，无需创建新头结点

```C++
class Solution {
public:
    ListNode* ReverseList(ListNode* pHead) {
        if(pHead == nullptr) return nullptr;
        
        ListNode *pre = nullptr;
        ListNode *next = nullptr;
        
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

<img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/0dae7e93-cfd1-4bd3-97e8-325b032b716f-1572687622947.gif" alt="img" style="zoom: 67%;" />

```C++
class Solution {
public:
    ListNode* ReverseList(ListNode* pHead) {
        if(pHead == nullptr) return nullptr;
        
        ListNode *newHead = new ListNode(-1); 
        ListNode *next = nullptr;
        while(pHead) {
            next = pHead->next;
           	pHead->next = newHead->next;
            newHead->next = pHead;
            pHead = next;
        }
        
        return newHead;
    }
};
```



方法3：递归

```C++
 ListNode* ReverseList(ListNode* pHead) {
    if (pHead == nullptr || pHead->next == nullptr)
        return head;
    ListNode *next = pHead->next;
    head->next = nullptr;
    ListNode *newHead = ReverseList(next);
    next->next = pHead;
    return newHead;
}
```



##### 面试题25 合并两个排序的链表（核心思想：递归/迭代）

【[OJ](https://www.nowcoder.com/practice/d8b6b4358f774294a89de2a6ac4d9337?tpId=13&tqId=11169&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】输入两个单调递增的链表，输出两个链表合成后的链表，合成后的链表满足单调不减规则。

**题解**：

方法1：递归

```C++
class Solution {
public:
    ListNode* Merge(ListNode* pHead1, ListNode* pHead2)
    {
        if(pHead1 == nullptr) // 链表1为空
            return pHead2; // 返回链表2
        else if(pHead2 == nullptr) // 链表2为空
            return pHead1; // 返回链表1
        
        ListNode *pMergedHead = nullptr; // 合并后链表头指针
        
        if(pHead1->val < pHead2->val) { // 链表1头元素小于链表2头元素
            pMergedHead = pHead1; // 链表1头作为合并后的头
            pMergedHead->next = Merge(pHead1->next, pHead2); // 剩余的链表按同样的方法合并
        } else { // 链表2头元素小于链表1头元素
            pMergedHead = pHead2; // 链表2头作为合并后的头
            pMergedHead->next = Merge(pHead1, pHead2->next); // 剩余的链表按同样的方法合并
        }
        
        return pMergedHead;
    }
};
```



方法2：迭代

```C++
class Solution {
public:
    ListNode* Merge(ListNode* pHead1, ListNode* pHead2)
    {
        ListNode *pHead = new ListNode(-1); // 创建节点以便于遍历
        ListNode *pCurr = pHead;
        while(pHead1 != nullptr && pHead2 != nullptr) {
            if(pHead1->val < pHead2->val) {
                pCurr->next = pHead1;
                pHead1 = pHead1->next;
            } else {
                pCurr->next = pHead2;
                pHead2 = pHead2->next;
            }
            pCurr = pCurr->next;
        }
        // 处理未遍历完的节点
        if(pHead1 != nullptr)
            pCurr->next = pHead1;
        if(pHead2 != nullptr)
            pCurr->next = pHead2;
        
        return pHead->next;
    }
};
```



##### 面试题35 复杂链表的复制✏️

【[OJ](https://www.nowcoder.com/practice/f836b2c43afc4b35ad6adc41ec941dba?tpId=13&tqId=11178&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

```C++

```



##### 面试题52 两个链表的第一个公共节点

【[OJ](https://www.nowcoder.com/practice/6ab1d9a29e88450685099d45c9e31e46?tpId=13&tqId=11189&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】输入两个链表，找出它们的第一个公共结点。

**题解**：

方法1：

可以先遍历一次得到两链表的长度差dist，第二次先在长的链表上走dist步，接下来同时遍历两个链表，直到找到它们第一个相同的节点，即为结果。

```C++
class Solution {
public:
    ListNode* FindFirstCommonNode( ListNode* pHead1, ListNode* pHead2) {
        ListNode* pLong = pHead1;
        ListNode* pShort = pHead2;
        unsigned int len1 = getListLen(pHead1);
        unsigned int len2 = getListLen(pHead2);
        unsigned int dist = len1 - len2;
        if(len1 < len2) {
            pLong = pHead1;
            pShort = pHead2;
            dist = len2 - len1;
        }
        
        for(int i = 0; i < dist; ++i) {
            pLong = pLong->next;
        }
        
        while(pLong && pShort) {
            if(pLong == pShort)
                return pLong;
            else {
                pLong = pLong->next;
                pShort = pShort->next;
            }
        }
        
        return pLong;
    }
    
    int getListLen(ListNode* pHead) {
        unsigned int len = 0;
        ListNode* pNode = pHead;
        while(pNode) {
            pNode = pNode->next;
            ++len;
        }
        
        return len;
    }
};
```



方法2：

设 A 的长度为 a + c，B 的长度为 b + c，其中 c 为尾部公共部分长度，可知 a + c + b = b + c + a。

当访问链表 A 的指针访问到链表尾部时，令它从链表 B 的头部重新开始访问链表 B；同样地，当访问链表 B 的指针访问到链表尾部时，令它从链表 A 的头部重新开始访问链表 A。这样就能控制访问 A 和 B 两个链表的指针能同时访问到交点。

```C++
class Solution {
public:
    // 此方法循环次数过多，未通过oj，但最简洁
    ListNode* FindFirstCommonNode( ListNode* pHead1, ListNode* pHead2) {
        ListNode* pNode1;
        ListNode* pNode2;
        
        while(pNode1 != pNode2) {
            pNode1 = (pNode1 == nullptr) ? pHead2 : pNode1->next;
            pNode2 = (pNode2 == nullptr) ? pHead1 : pNode2->next;
        }
        
        return pNode1;
    }
};
```



#### 队列、堆、栈

##### 面试题9 用两个栈实现队列（核心思想：双栈）

【[OJ](https://www.nowcoder.com/practice/54275ddae22f475981afa2244dd448c6?tpId=13&tqId=11158&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】用两个栈来实现一个队列，完成队列的Push和Pop操作。

**题解**：元素直接放入栈1，栈2为空时从栈1取出所有元素放入栈2

```C++
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



##### 面试题30 包含min函数的栈（核心思想：双栈）

【[OJ](https://www.nowcoder.com/practice/4c776177d2c04c2494f2555c9fcc1e49?tpId=13&tqId=11173&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数，时间复杂度为O(1)。

题解：元素直接压入主栈，仅当辅助栈为空或当前值小于等于辅助栈顶时，压入辅助栈。弹出时主栈栈顶元素等于辅助栈栈顶时，弹出辅助栈栈顶。

```C++
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
        // assert(!mstack.empty());
        return mstack.top();
    }
    int min() {
        // assert(!help.empty());
        return help.top();
    }
};
```



##### 面试题31 栈的压入、弹出序列（核心思想：辅助栈）

【[OJ](https://www.nowcoder.com/practice/d77d11405cc7470d82554cb392585106?tpId=13&tqId=11174&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）

**题解**：使用一个辅助栈来模拟压入弹出操作，辅助栈中逐个压入push中元素并与pop当前元素比较，若相等则弹出，直至不等，若栈最后为空则弹出序列正确， 剑指offer书上的题解过于复杂。

```C++
class Solution {
public:
    bool IsPopOrder(vector<int> pushV,vector<int> popV) {
        if (pushV.empty() && popV.empty()) return false;

        stack<int> stackData;
        int pNextPush = 0, pNextPop = 0;
        while (pNextPush < pushV.size()) {
            stackData.push(pushV[pNextPush++]); // 逐个将pushV中元素压入辅助栈
            // 当前辅助栈栈顶与popV下一元素相等
            while (!stackData.empty() && stackData.top() == popV[pNextPop]) {
                stackData.pop(); // 弹出辅助栈栈顶
                pNextPop++; // 继续与popV中下一个元素比较
            }
        }
        // 若辅助栈元素全部弹出，证明弹出序列可能
        return stackData.empty();
    }
};
```



##### 面试题59.1 滑动窗口的最大值（核心思想：双端队列）

【[OJ](https://www.nowcoder.com/practice/1624bc35a45c42c0bc17d17fa0cba788?tpId=13&tqId=11217&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】给定一个数组和滑动窗口的大小，找出所有滑动窗口里数值的最大值。例如，如果输入数组{2,3,4,2,6,2,5,1}及滑动窗口的大小3，那么一共存在6个滑动窗口，他们的最大值分别为{4,4,6,6,6,5}； 针对数组{2,3,4,2,6,2,5,1}的滑动窗口有以下6个： {[2,3,4],2,6,2,5,1}， {2,[3,4,2],6,2,5,1}， {2,3,[4,2,6],2,5,1}， {2,3,4,[2,6,2],5,1}， {2,3,4,2,[6,2,5],1}， {2,3,4,2,6,[2,5,1]}。

**题解**：使用双端队列存储可能最大的元素下标。

```C++
class Solution {
public:
    // 使用双端队列存储可能最大的元素下标
    vector<int> maxInWindows(const vector<int>& num, unsigned int size)
    {
        vector<int> res;
        deque<int> dq;
        
        if(num.size() < size || size <= 0)
            return  res;
        
        for(int i = 0; i < num.size(); ++i){
            //从后面依次弹出队列中比当前num值小的元素，同时保证队列首元素为当前窗口最大值下标
            while(dq.size() && num[i] >= num[dq.back()])
                dq.pop_back();
            
            //当前窗口移出队首元素所在的位置，即队首元素坐标对应的num不在窗口中，需要弹出
            while(dq.size() && i-dq.front()+1 > size)
                dq.pop_front();
            
            dq.push_back(i);//把每次滑动的num下标加入队列
            
            if(size && i >= size - 1)//当滑动窗口首地址i大于等于size时才开始写入窗口最大值
                res.push_back(num[dq.front()]);
        }
        return res;
    }
    
    // 暴力求解
    vector<int> maxInWindows_(const vector<int>& num, unsigned int size)
    {
        vector<int> res;
        if(num.size() < size || size <= 0)
            return  res;
        
        for(int i = 0; i <= num.size() - size; ++i) {
            res.push_back(getMax(num, i, i + size - 1));
        }
        
        return res;
    }
    
    int getMax(vector<int> num , int low, int high) {
        int max = 0;
        for(int i = low; i <= high; ++i) {
            if(num[i] > max)
                max = num[i];
        }
        
        return max;
    }
};
```



##### 面试题41 数据流中的中位数（核心思想：堆）

【[OJ](https://www.nowcoder.com/practice/9be0172896bd43948f8a32fb954e1be1?tpId=13&tqId=11216&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】如何得到一个数据流中的中位数？如果从数据流中读出奇数个数值，那么中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，那么中位数就是所有数值排序之后中间两个数的平均值。我们使用Insert()方法读取数据流，使用GetMedian()方法获取当前读取数据的中位数。

**题解**：使用大顶堆维护左半边数组，使用小顶堆维护右半边数组。

C++中使用堆，可利用STL中的[priority_queue](http://c.biancheng.net/view/480.html)

```C++
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



#### 二叉树

##### 面试题7 重建二叉树（核心思想：递归）⭐️

【[OJ](https://www.nowcoder.com/practice/8a19cbe657394eeaac2f6ea9b0f6fcf6?tpId=13&tqId=11157&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。

<img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/image-20191102210342488.png" alt="img" style="zoom:67%;" />



**题解**：前序遍历的第一个值为根节点的值，使用这个值将中序遍历结果分成两部分，左部分为树的左子树中序遍历结果，右部分为树的右子树中序遍历的结果。然后分别对左右子树递归地求解。

<img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/60c4a44c-7829-4242-b3a1-26c3b513aaf0.gif" alt="img" style="zoom:67%;" />

可利用中序遍历

```C++
class Solution {
public:
    TreeNode* reConstructBinaryTree(vector<int> pre,vector<int> vin) {
        if(pre.size() == 0 || vin.size() == 0) { // 检查是否为空
            return nullptr;
        }
        return reConstructBinaryTree(pre,0,pre.size()-1,vin,0,vin.size()-1);
    }

    TreeNode* reConstructBinaryTree(vector<int> &pre,int startPre,int endPre,vector<int> &in,int startIn,int endIn) {
        if(startPre > endPre || startIn > endIn) // 递归到了根节点
            return nullptr;
        
        TreeNode* root = new TreeNode(pre[startPre]);//创建根节点，根节点为先序序列首元素
        for(int i = startIn; i <= endIn; i++) {
            if (in[i] == pre[startPre]) { // 寻找根节点在中序遍历序列中的位置
                int llen = i - startIn; // 利用根节点在中序遍历序列中的位置计算左子树节点数
                // 左子树节点在先序遍历序列中的范围为[startPre+1,startPre+llen]
                // 左子树节点在中序遍历序列中的范围为[startIn,i-1]
                root->left = reConstructBinaryTree(pre, startPre + 1, startPre + llen, in, startIn, i - 1);
                // 右子树节点在先序遍历序列中的范围为[startPre+llen+1,endPre]
                // 右子树节点在中序遍历序列中的范围为[i+1,endIn]
                root->right = reConstructBinaryTree(pre, startPre + llen + 1, endPre, in, i + 1, endIn);
                break;
            }

//            if (i == endIn) // 未找到根节点在中序遍历序列中的位置
//                cout << "input error" << endl;
        }
                 
        return root;
    }
};
```



剑指offer写法（超繁琐，但使用指针操作，占用内存更少）：

```C++
class Solution {
public:
    TreeNode* reConstructBinaryTree(vector<int> pre,vector<int> vin) {
        if(pre.size() == 0 || vin.size() == 0) { // 检查是否为空
            return nullptr;
        }
        
        int *preStart = &pre[0];
        int *preEnd = &pre[pre.size() - 1];
        int *vinStart = &vin[0];
        int *vinEnd = &vin[vin.size() - 1];
        
        return ConstructCore(preStart, preEnd, vinStart, vinEnd); // 递归重建二叉树
    }
    
    TreeNode* ConstructCore(int *preStart, int *preEnd, int *vinStart, int *vinEnd) {
        // 创建根节点
        TreeNode* root = new TreeNode(*preStart);
        
        if(preStart == preEnd) {
            if(vinStart == vinEnd && *preStart == *vinStart) { // 仅有一个节点
                return root;
            }
            //else
                //throw(std::exception("Invalid input!"));
        }
        
        int *vinRoot = vinStart; // 初始化后续遍历中根节点指针
        while(vinRoot <= vinEnd && *vinRoot != *preStart) // 查找后续遍历中根节点位置
            ++vinRoot;
        
        // 后续遍历中未找到根节点
        //if(vinRoot == vinEnd && *vinRoot != rootVal)
            //throw(std::exception("Invalid input!"));
        
        int leftLength = vinRoot - vinStart; // 左子树长度
        int *leftPreorderEnd = preStart + leftLength; // 先序遍历中左子树末尾元素指针
        if(leftLength > 0) { // 存在左子树
            // 递归构建左子树
            root->left = ConstructCore(preStart + 1, leftPreorderEnd, vinStart, vinRoot - 1);
        }
        
        if(leftLength < preEnd - preStart) {
            // 递归构建右子树
            root->right = ConstructCore(leftPreorderEnd + 1, preEnd, vinRoot + 1, vinEnd);
        }
        
        return root;
    }
};
```



##### 面试题8 二叉树的下一个结点

【[OJ](https://www.nowcoder.com/practice/9023a0c988684a53960365b889ceaf5e?tpId=13&tqId=11210&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】给定一个二叉树和其中的一个结点，请找出中序遍历顺序的下一个结点并且返回。注意，树中的结点不仅包含左右子结点，同时包含指向父结点的指针。

**题解**：

① 如果一个节点的右子树不为空，那么该节点的下一个节点是右子树的最左节点；

<img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/7008dc2b-6f13-4174-a516-28b2d75b0152.gif" alt="img" style="zoom:67%;" />



② 否则，向上找第一个左链接指向的树包含该节点的祖先节点。

<img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/094e3ac8-e080-4e94-9f0a-64c25abc695e.gif" alt="img" style="zoom:67%;" />

```C++
class Solution {
public:
    TreeLinkNode* GetNext(TreeLinkNode* pNode)
    {
        if(pNode == nullptr)
            return nullptr;
        
        // 节点有右孩子：1-右孩子无左孩子，下一节点即为右孩子 2-右孩子有左孩子，则左子树的最左子节点为下一节点
        if(pNode->right != nullptr) { // 此节点有右孩子
            pNode = pNode->right; // 记录右孩子
            while(pNode->left != nullptr) { // 寻找右子树的最左子节点
                pNode = pNode->left;
            }
            return pNode;
        }
        // 节点无右孩子：1-当前节点为双亲节点的左子节点，则下一节点即为双亲节点 2-当前节点为双亲节点的右子节点，须向上寻找，找到当前节点为双亲节点的左子节点为止，此时双亲节点为下一节点
        else { // 有双亲节点
            TreeLinkNode* pParent = nullptr; // 记录双亲节点
            // 向上遍历，寻找当前节点是双亲的左子节点
            while(pNode->next != nullptr) { // 双亲节点不为空，即未遍历到根节点
                pParent = pNode->next;
                if(pParent->left == pNode)
                    return pParent;
                pNode = pNode->next;
            }
        }
        
        return nullptr;
    }
};
```



剑指offer解法，稍繁琐

```C++
class Solution {
public:
    TreeLinkNode* GetNext(TreeLinkNode* pNode)
    {
        if(pNode == nullptr)
            return nullptr;
        
        TreeLinkNode* pNext = nullptr; // 指向下一节点的指针
        
        // 节点有右孩子：1-右孩子无左孩子，下一节点即为右孩子 2-右孩子有左孩子，则左子树的最左子节点为下一节点
        if(pNode->right != nullptr) { // 此节点有右孩子
            TreeLinkNode* pRight = pNode->right; // 记录右孩子
            while(pRight->left != nullptr) { // 寻找右子树的最左子节点
                pRight = pRight->left;
            }
            
            pNext = pRight;
        }
        // 节点无右孩子：1-当前节点为双亲节点的左子节点，则下一节点即为双亲节点 2-当前节点为双亲节点的右子节点，须向上寻找
        else if(pNode->next != nullptr) { // 有双亲节点
            TreeLinkNode* pCurrent = pNode; // 记录当前节点
            TreeLinkNode* pParent = pNode->next; // 记录双亲节点
            while(pParent != nullptr && pCurrent == pParent->right) { // 向上遍历，寻找当前节点是双亲的左子节点
                pCurrent = pParent;
                pParent = pParent->next;
            }
            
            pNext = pParent; // 当前节点的双亲是下一个节点
        }
        
        return pNext;
    }
};
```



##### 面试题26 树的子结构（递归）⭐️

【[OJ](https://www.nowcoder.com/practice/6e196c44c7004d15b1610b9afca8bd88?tpId=13&tqId=11170&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】输入两棵二叉树A，B，判断B是不是A的子结构。

<img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/84a5b15a-86c5-4d8e-9439-d9fd5a4699a1.jpg" alt="img" style="zoom:67%;" />

**题解**：

第一步：在A树中查找与B树根节点值相等的节点，递归

第二步：找到相等节点后，逐个判断相等节点左右子树中的各节点值是否相等， 递归

```C++
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



##### 面试题27 二叉树的镜像（核心思想：递归）

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

```C++
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



##### 面试题28 对称的二叉树（核心思想：递归）⭐️

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

```C++
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




##### 面试题32.1 从上往下打印二叉树

【[OJ](https://www.nowcoder.com/practice/7fe2212963db4790b57431d9ed259701?tpId=13&tqId=11175&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】从上往下打印出二叉树的每个节点，同层节点从左至右打印。

**题解**：使用队列保存将要打印的节点，节点出队列时将其左右子节点加入队列

```C++
class Solution {
public:
    // 通用写法，隐含分层思想，使用两个循环
	vector<int> PrintFromTopToBottom(TreeNode* root) {
        vector<int> res;
        queue<TreeNode*> nodeQueue;

        if(root == nullptr) return res;

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
        vector<int> res;
        queue<TreeNode*> nodeQueue;

        if(root == nullptr) return res;

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



##### 面试题32.2 把二叉树打印成多行（分行从上到下打印二叉树）

【[OJ](https://www.nowcoder.com/practice/445c44d982d04483b04a54f298796288?tpId=13&tqId=11213&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】从上到下按层打印二叉树，同一层结点从左至右输出。每一层输出一行。

**题解**：要求一行打印一层，因此需要在每层打印完时记录下一层要打印的节点个数

```C++
class Solution {
public:    
	// 简单方法，使用两层循环
    vector<vector<int> > Print(TreeNode* pRoot) {
        vector<vector<int>> res;
        queue<TreeNode*> nodeQueue;

        if(pRoot == nullptr) return res;

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

    // 剑指offer解法，设置两个变量，较繁琐
    vector<vector<int> > Print_(TreeNode* pRoot) {
        vector<vector<int>> ret;
        vector<int> tmp;

        if(!pRoot)
            return ret;

        queue<TreeNode *> queueNode;
        queueNode.push(pRoot);
        int num = 0; // 各层元素数量计数
        int toBePrint = 1; // 需打印的各层元素数量

        while(!queueNode.empty()) {
            TreeNode *p = queueNode.front();
            queueNode.pop(); // 取出队头元素

            //printf("%d ", p->val);
            tmp.push_back(p->val);

            if(p->left != nullptr) {
                queueNode.push(p->left);
                ++num;
            }

            if(p->right != nullptr) {
                queueNode.push(p->right);
                ++num;
            }

            --toBePrint;
            if(toBePrint == 0) { // 一行打印完
                ret.push_back(tmp);
                tmp.clear();

                toBePrint = num; // 下一行元素数量
                num = 0; // 重新记录需要打印的元素个数
            }
        }

        return ret;
    }
};
```



##### 面试题32.3 按之字形顺序打印二叉树

【[OJ](https://www.nowcoder.com/practice/91b69814117f4e8097390d107d2efbe0?tpId=13&tqId=11212&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】请实现一个函数按照之字形打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右至左的顺序打印，第三行按照从左到右的顺序打印，其他行以此类推。

**题解**：

**方法1**：使用queue和vector，将每行节点值保存到vector，需要逆序时使用reverse将vector逆序再打印

```C++
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

```C++
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

```C++
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



##### 面试题33 二叉搜索树的后序遍历序列

【[OJ](https://www.nowcoder.com/practice/a861533d45854474ac791d90e447bafd?tpId=13&tqId=11176&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

```C++
class Solution {
public:
    bool VerifySquenceOfBST(vector<int> sequence) {
        return Verify(sequence, 0, sequence.size());
    }
    
    bool Verify(vector<int> sequence, int begin, int end) {
        if(sequence.empty() || begin > end) return false;
        
        int i = begin;
        int root = sequence[end];
        
        // 寻找左边第一个比根节点大的值，此值前为左子树，后为右子树
        for(; i < end; ++i) {
            if(sequence[i] > root)
                break;
        }
        
        // 右子树各节点的值应大于根节点
        for(int j = i; i < end; ++i) {
            if(sequence[i] < root)
                return false;
        }
        
        // 判断左子树是不是BST
        bool left = true;
        if(i > begin)
            left = Verify(sequence, begin, i-1);
        // 判断右子树是不是BST
        bool right = true;
        if(i < end - 1)
            right = Verify(sequence, i, end);
        
        return (left && right);
    }
};
```



##### 面试题34 二叉树中和为某一值的路径

【[OJ](https://www.nowcoder.com/practice/b736e784e3e34731af99065031301bca?tpId=13&tqId=11177&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

```C++
class Solution {
public:
    vector<vector<int> > allPath;
    vector<int> path;
    
    vector<vector<int> > FindPath(TreeNode* root, int expectNumber) {
        if(!root) return allPath;

        path.push_back(root->val);
        
        bool isLeaf = root->left == nullptr && root->right == nullptr;
        if(isLeaf && (expectNumber - root->val == 0)) { // 是叶子节点，并满足要求
            allPath.push_back(path); // 保存此路径
        }
        
        // 不是叶子节点则遍历其子节点，想要的值为expectNumber - root->val
        if(root->left != nullptr)
            FindPath(root->left, expectNumber - root->val); 
        if(root->right != nullptr)
            FindPath(root->right, expectNumber - root->val);
        
        // 返回父节点之前，在路径上删除当前节点
        path.pop_back();
        
        return allPath;
    }
};
```



##### 面试题36 二叉搜索树与双向链表⚔️

【[OJ](https://www.nowcoder.com/practice/947f6eb80d944a84850b0538bf0ec3a5?tpId=13&tqId=11179&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

```C++
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



##### 面试题37 序列化二叉树⚔️

【[OJ](https://www.nowcoder.com/practice/cf7e25aa97c04cc1a68c8f040e71fb84?tpId=13&tqId=11214&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

```C++
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



##### 面试题55.1 二叉树的深度

【[OJ](https://www.nowcoder.com/practice/435fb86331474282a3499955f0a41e8b?tpId=13&tqId=11191&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

```C++
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



##### 面试题55.2 平衡二叉树

【[OJ](https://www.nowcoder.com/practice/8b3b95850edb4115918ecebdf1b4d222?tpId=13&tqId=11192&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

后序遍历，并在遍历左右子树的同时，实时记录并比较左右子树的深度

```C++
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



##### 面试题54 二叉搜索树的第K大节点（二叉搜索树的第k个结点）

【[OJ](https://www.nowcoder.com/practice/ef068f602dde4d28aab2b210e859150a?tpId=13&tqId=11215&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

二叉搜索树的中序遍历结果即从小到大依次排列

```C++
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



#### 字符串

##### 面试题5 替换空格

【[OJ](https://www.nowcoder.com/practice/4060ac7e3e404ad1a894ef3e17650423?tpId=13&tqId=11155&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】请实现一个函数，将一个字符串中的每个空格替换成“%20”。例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy。

```C++
class Solution {
public:
	void replaceSpace(char *str,int length) {
        int blancks_cnt = 0;
        int str_length = 0;
        for (int i = 0; str[i] != '\0'; i++) { // 统计字符串实际长度及空格数
            str_length ++;
            if (str[i] == ' ') {
                blancks_cnt++;
            }
        }
        
        int after_len = str_length + 2*blancks_cnt; // 替换后字符串长度
        if (str_length+1 > length) return ; // 长度输入有误
        char *pEnd = str + str_length; // 指向原字符串'\0'
        char *pEndNew = str + after_len; // 指向新字符串的'\0'
        while (pEnd < pEndNew) { // 是空格，替换
            if (*pEnd == ' ') {
                *pEndNew-- = '0';
                *pEndNew-- = '2';
                *pEndNew-- = '%';
            } else { // 不是空格，填入原始数据
                *pEndNew-- = *pEnd;
            }
            
            --pEnd;
        }
	}
};
```



##### 面试题19 正则表达式匹配

【[OJ](https://www.nowcoder.com/practice/45327ae22b7b413ea21df13ee7d6429c?tpId=13&tqId=11205&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】请实现一个函数用来匹配包括'.'和'*'的正则表达式。模式中的字符'.'表示任意一个字符，而'*'表示它前面的字符可以出现任意次（包含0次）。 在本题中，匹配是指字符串的所有字符匹配整个模式。例如，字符串"aaa"与模式"a.a"和"ab*ac*a"匹配，但是与"aa.a"和"ab*a"均不匹配

```C++
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



##### 面试题20 表示数值的字符串

【[OJ](https://www.nowcoder.com/practice/6f8c901d091949a5837e24bb82a731f2?tpId=13&tqId=11206&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】请实现一个函数用来判断字符串是否表示数值（包括整数和小数）。例如，字符串"+100","5e2","-123","3.1416"和"-1E-16"都表示数值。 但是"12e","1a3.14","1.2.3","+-5"和"12e+4.3"都不是。



字符串"+100","5e2","-123","3.1416"和"-1E-16"都表示数值。 但"12e","1a3.14","1.2.3","+-5"和"12e+4.3"都不是。

- e后面一定要接数字
- 不能同时存在两个e
- 第一次出现+-符号，且不是在字符串开头，则必须紧接在e之后
- 第二次出现+-符号，则必须紧接在e之后
- e后面不能接小数点，小数点不能出现两次
- 不可有不合法字符

```C++
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



##### 面试题38 字符串的排列

【[OJ](https://www.nowcoder.com/practice/fe6b651b66ae47d7acce78ffdd9a96c7?tpId=13&tqId=11180&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】输入一个字符串,按字典序打印出该字符串中字符的所有排列。例如输入字符串abc,则打印出由字符a,b,c所能排列出来的所有字符串abc,acb,bac,bca,cab和cba。

从集合中依次选出每一个元素，作为排列的第一个元素，然后对剩余的元素进行全排列，如此递归处理，从而得到所有元素的全排列。以对字符串abc进行全排列为例，我们可以这么做：以abc为例

- 固定a，求后面bc的排列：abc，acb，求好后，a和b交换，得到bac
- 固定b，求后面ac的排列：bac，bca，求好后，c放到第一位置，得到cba
- 固定c，求后面ba的排列：cba，cab。

```C++
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



#####  面试题50.1 字符串中第一个只出现一次的字符（第一个只出现一次的字符）

【[OJ](https://www.nowcoder.com/practice/1c82e8cf713b4bbeb2a5b31cf5b0417c?tpId=13&tqId=11187&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】在一个字符串(0<=字符串长度<=10000，全部由字母组成)中找到第一个只出现一次的字符,并返回它的位置, 如果没有则返回 -1（需要区分大小写）.

最直观的解法是使用 HashMap 对出现次数进行统计，但是考虑到要统计的字符范围有限，因此可以使用char型数组代替 HashMap，从而将空间复杂度由 O(N) 降低为 O(1)。

```C++
class Solution {
public:
    int FirstNotRepeatingChar(string str) {
        if(str.empty())
            return -1;
        
        char ch[256]={0}; // 使用一字节的char数组保存各字符出现的次数
        for(int i = 0; i < str.size(); i++) // 统计各字符出现次数
            ch[str[i]]++;
        for(int i = 0; i < str.size(); i++) // 遍历次数数组，找到次数为1的位置
            if(ch[str[i]] == 1)
                return i;
        return 0;
    }
};
```



##### 面试题50.2 字符流中第一个只出现一次的字符（字符流中第一个不重复的字符）

【[OJ](https://www.nowcoder.com/practice/00de97733b8e4f97a3fb5c680ee10720?tpId=13&tqId=11207&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】请实现一个函数用来找出字符流中第一个只出现一次的字符。例如，当从字符流中只读出前两个字符"go"时，第一个只出现一次的字符是"g"。当从该字符流中读出前六个字符“google"时，第一个只出现一次的字符是"l"。

**方法1**：使用queue保存出现过的字符，使用数组保存各字符出现的次数，判断队头是否只出现一次，时间复杂度O(1)，空间复杂度O(n)

1. 用一个128大小的数组统计每个字符出现的次数 
2. 用一个队列，如果第一次遇到ch字符，则插入队列；其他情况不在插入 
3. 求解第一个出现的字符，判断队首元素是否只出现一次，如果是直接返回，否则删除继续第3步骤

**方法2**：使用数组作为哈希表，统计各字符出现次数，时间复杂度O(n)，空间复杂度O(n)

```C++
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

##### 面试题58.1 翻转单词顺序（翻转单词顺序列）

【[OJ](https://www.nowcoder.com/practice/3194a4f4cf814f63919d0790578d51f3?tpId=13&tqId=11197&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】牛客最近来了一个新员工Fish，每天早晨总是会拿着一本英文杂志，写些句子在本子上。同事Cat对Fish写的内容颇感兴趣，有一天他向Fish借来翻看，但却读不懂它的意思。例如，“student. a am I”。后来才意识到，这家伙原来把句子单词的顺序翻转了，正确的句子应该是“I am a student.”。Cat对一一的翻转这些单词顺序可不在行，你能帮助他么？

先翻转整个字符串，再分别翻转以空格分割的子字符串即可

```C++
class Solution {
public:
    string ReverseSentence(string str) {
        if(str.empty())
            return str;
        
        Reverse(str, 0, str.size() - 1); // 翻转整个字符串
        
        int start = 0; // 记录一段无空格字符串的开始，第一段字符串从0开始
        for(int i = 0; i < str.size(); ++i) {
            if(str[i] == ' ') { // 遇到空格
                Reverse(str, start, i - 1); // 翻转开始与空格之前的字符串
                start = i + 1; // 下一段字符串开始为空格之后
            }
        
            if(i == str.size() - 1) // 最后一段字符串结尾是str.size() - 1
                Reverse(str, start, i);
        }
    
        return str;
    }
    
    void Reverse(string &str, int i, int j) {
        while(i < j) {
            swap(str[i++], str[j--]);
        }
    }
};
```



##### 面试题58.2 左旋转字符串

【[OJ](https://www.nowcoder.com/practice/12d959b108cb42b1ab72cef4d36af5ec?tpId=13&tqId=11196&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】汇编语言中有一种移位指令叫做循环左移（ROL），现在有个简单的任务，就是用字符串模拟这个指令的运算结果。对于一个给定的字符序列S，请你把其循环左移K位后的序列输出。例如，字符序列S=”abcXYZdef”,要求输出循环左移3位后的结果，即“XYZdefabc”。是不是很简单？OK，搞定它！

以“abcdefg”为例，可以把它分为两部分。由于想把它的前两个字符移到后面，我们就把前两个字符分到第一部分，把后面的所有字符分到第二部分。我们先分别翻转这两部分，于是就得到“bagfedc”。接下来翻转整个字符串，得到的"cdefgab“刚好就是把原始字符串左旋转两位的结果。

```C++
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



##### 面试题67 把字符串转换成整数

【[OJ](https://www.nowcoder.com/practice/1277c681251b4372bdef344468e4f26e?tpId=13&tqId=11202&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

需要考虑的情况：空字符串、正负号、非数字字符、整型数据溢出等

最大的正整数值是0x7FFF FFFF，最小的负整数是0x8000 0000

```C++
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



#### 位运算

##### 面试题15 二进制中1的个数

【[OJ](https://www.nowcoder.com/practice/8ee967e43c2c4ec193b040ea7fbb10b8?tpId=13&tqId=11164&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

```C++
class Solution {
public:
    // 循环次数等于整数二进制位中1的个数
    int  NumberOf1(int n) {
        int cnt = 0;
        while(n) {
            cnt++;

            n = n&(n-1);
        }

        return cnt;
    }
    
    // 循环次数等于整数二进制位的个数
    int  NumberOf1_(int n) {
         int cnt = 0;
         unsigned int flag = 1;
         while(n) {
             if(n&flag)
                 cnt++;
             
             flag = flag << 1;
         }
         
         return cnt;
     }
};
```



##### 面试题56.1 数组中只出现一次的两个数字（数组中只出现一次的数字）

【[OJ](https://www.nowcoder.com/practice/e02fdb54d7524710a7d664d082bb7811?tpId=13&tqId=11193&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

```C++
class Solution {
public:
    void FindNumsAppearOnce(vector<int> data,int* num1,int *num2) {
        if(data.size() < 2)
            return;
        
        int resultOr = 0;
        for(int i = 0; i < data.size(); ++i) {
            resultOr ^= data[i];
        }
        
        int indexOf1 = FindFirstBit1(resultOr);
        
        *num1 = *num2 = 0;
        for(int j = 0; j < data.size(); ++j) {
            if(IsBit1(data[j], indexOf1)) {
                *num1 ^= data[j];
            } else {
                *num2 ^= data[j];
            }
        }
    }
    
    int FindFirstBit1(int num) {
        int indexBit = 0;
        while(((num & 1) == 0) && (indexBit < 8 * sizeof(int))) {
            num = num >> 1;
            ++indexBit;
        }
        return indexBit;
    }
    
    bool IsBit1(int num, int indexBit) {
        num = num >> indexBit;
        return (num & 1);
    }
};
```



#### 数学

##### 面试题43  1~n整数中1出现的次数

【[OJ](https://www.nowcoder.com/practice/bd7f978302044eee894445e244c7eee6?tpId=13&tqId=11184&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

[Leetcode : 233. Number of Digit One](https://leetcode.com/problems/number-of-digit-one/discuss/64381/4+-lines-O(log-n)-C++JavaPython)

```C++
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



##### 面试题45 把数组排成最小的数

【[OJ](https://www.nowcoder.com/practice/8fecd3f8ba334add803bf2a06af1b993?tpId=13&tqId=11185&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

两个int 型的整数拼接起来得 到的数字可能会超出 int 型数字能够表达的范围，从而导致数字溢出。可以用字符串表示数字，这样就能简捷地解决大数问题 。

可以看成是一个排序问题，先将数字转换为字符串，在比较两个字符串 S1 和 S2 的大小时，应该比较的是 S1+S2 和 S2+S1 的大小，如果 S1+S2 < S2+S1，那么应该把 S1 排在前面，否则应该把 S2 排在前面。

```C++
class Solution {
public:
    string PrintMinNumber(vector<int> numbers) {
        int len = numbers.size();
        if(len == 0) return "";
        
        sort(numbers.begin(), numbers.end(), cmp); // 根据转换为字符串后的和进行排序
        
        string res;
        for(int i = 0; i < len; i++){
            res += to_string(numbers[i]);
        }
        return res;
    }
    
    static bool cmp(int a, int b){ // 转换为字符串求和并比较
        string A = to_string(a) + to_string(b);
        string B = to_string(b) + to_string(a);
        return A < B;
    }
};
```



##### 面试题49 丑数

【[OJ](https://www.nowcoder.com/practice/6aa9e04fc3794f68acf8778237ba065b?tpId=13&tqId=11186&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

所谓一个数 m 是另 一个数 n 的 因子，是指 n 能被 m 整除 也就是 n%m =0 。根据丑数的定义，丑数只能被 2 、3 和 5 整除 。也就是说，如果 一 个数能被 2 整除，就连续除以 2；如果能被 3 整除，就连续除以3；如果能被 5整除，就除以连续 5。如果最后得到的是1，那么这个数就是丑数，否则不是 。

假设数组中已经有若下个排好序的且数，并且把已有最大的丑数记作M，下一个丑数肯定是前面某一个丑数乘以2、3或者5的结果。对于乘以2而言，肯定存在某一个丑数T2，排在它之前的每个丑数乘以2得到的结果都会小于已有最大的丑数，在它之后的每个丑数乘以2得到的结果都会太大。我们只需记下这个丑数的位置，同时每次生成新的丑数的时候去更新这个T2即可。对千乘以3和5而言，也存在同样的T3和T5。

```C++
class Solution {
public:
    int GetUglyNumber_Solution(int index) {
        if (index <= 6)
            return index;
        int i2 = 0, i3 = 0, i5 = 0;
        vector<int> dp(index);
        dp[0] = 1; // 按顺序保存已经生成的丑数
        for (int i = 1; i < index; i++) {
            int next2 = dp[i2] * 2, next3 = dp[i3] * 3, next5 = dp[i5] * 5;
            dp[i] = min(next2, min(next3, next5)); // 保存下一个可能的丑数中最小的
            // 在已得到的丑数基础上查找下一个丑数
            if (dp[i] == next2) i2++;
            if (dp[i] == next3) i3++;
            if (dp[i] == next5) i5++;
        }
        return dp[index - 1];
    }
};
```



#### 递归

##### 面试题10.1 斐波那契数列

【[OJ](https://www.nowcoder.com/practice/c6c7742f5ba7442aada113136ddea0c3?tpId=13&tqId=11160&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

```C++
class Solution {
public:
    int Fibonacci(int n) {
        if (n <= 1) return n;
        
        int first = 0, second = 1, sum = 0;
        for(int i = 2; i <= n; i++) {
            sum = first + second;
            
            // 注意赋值顺序
            first = second;
            second = sum;
        }
        
        return sum;
    }
};
```



##### 面试题10.2 青蛙跳台阶问题（跳台阶）

【[OJ](https://www.nowcoder.com/practice/8c82a5b80378478f9484d87d1c5f12a4?tpId=13&tqId=11161&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

```C++
class Solution {
public:
    int jumpFloor(int number) {
        if(number <= 2) {
            return number;
        }
        
        int first = 1, second = 2, next = 0;
        for(int i = 3; i <= number; ++i) {
            next = first + second;
            
            first = second;
            second = next;
        }
        
        return next;
    }
};
```



##### 面试题10 扩展  变态跳台阶

【[OJ](https://www.nowcoder.com/practice/22243d016f6b47f2a6928b4313c85387?tpId=13&tqId=11162&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

```C++
class Solution {
public:
    int jumpFloorII(int number) {
        if(number == 0)
            return 0;
        
        int total = 1;
        for(int i=1; i < number; i++) // 2^(n-1)
            total*=2;
        
        return total;
    }
};
```



##### 面试题10 相关题目  矩形覆盖

【[OJ](https://www.nowcoder.com/practice/72a5a919508a4251859fb2cfb987a0e6?tpId=13&tqId=11163&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

```C++
class Solution {
public:
    int rectCover(int number) {
        if(number <= 0){
            return 0;
        }else if(number <= 2){
            return number;
        }
        
        int first = 1, second = 2, next = 0;
        for(int i = 3; i <= number; ++i){
            next = first + second;
            
            first = second;
            second = next;
        }
        
        return next;
    }
};
```



##### 面试题16 数值的整数次方

【[OJ](https://www.nowcoder.com/practice/1a834e5e3e1a4b7ba251417554e07c00?tpId=13&tqId=11165&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

```C++
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



#### 回溯法

##### 面试题12 矩阵中的路径

【[OJ](https://www.nowcoder.com/practice/c61c6999eecb4b8f88a98f66b273a3cc?tpId=13&tqId=11218&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

```C++
class Solution {
public:
    bool hasPath(char* matrix, int rows, int cols, char* str)
    {
        if(matrix == nullptr || str == nullptr || rows < 1 || cols < 1)
            return false;
        
        bool *visited = new bool[rows*cols];
        memset(visited, 0, rows*cols);
        int pathLen = 0;
        
        for(int row = 0; row < rows; ++row) {
            for(int col = 0; col < cols; ++col) {
                if(hasPathCore(matrix, rows, cols, row, col, str, visited, pathLen)){
                    return true;
                }
            }
        }
        
        delete[] visited;
        
        return false;
    }

    bool hasPathCore(char* matrix, int rows, int cols, int row, int col, char* str, bool* visited, int& pathLen) {
        if(str[pathLen] == '\0')
            return true;
        
        bool hasPath = false;
        if(row >= 0 && row < rows && col >= 0 && col < cols && matrix[row*cols + col] == str[pathLen] 
                    && !visited[row*cols + col]) {
            ++pathLen;
            
            visited[row*cols + col] = true;
            
            hasPath = hasPathCore(matrix, rows, cols, row, col - 1, str, visited, pathLen) ||
                      hasPathCore(matrix, rows, cols, row - 1, col, str, visited, pathLen) ||
                      hasPathCore(matrix, rows, cols, row, col + 1, str, visited, pathLen) ||
                      hasPathCore(matrix, rows, cols, row + 1, col, str, visited, pathLen);
            
            if(!hasPath) {
                --pathLen;
                visited[row*cols + col] = false;
            }

        }
        
        return hasPath;
    }

};
```



##### 面试题13 机器人的运动范围

【[OJ](https://www.nowcoder.com/practice/6e5207314b5241fb83f2329e89fdecc8?tpId=13&tqId=11219&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

```C++
class Solution {
public:
    int movingCount(int threshold, int rows, int cols)
    {
        int cnt = 0;

        bool *visited = new bool[rows * cols];
        for(int i = 0; i < rows * cols; ++i) {
            visited[i] = false;
        }

        movingCore(threshold, rows, cols, 0, 0, cnt, visited);

        delete[] visited;
        return cnt;
    }

    void movingCore(int threshold, int rows, int cols, int row, int col, int& cnt, bool *visited) {

        if(row >= 0 && row < rows && col >= 0 && col < cols && sumValid(row, col, threshold) && !visited[row*cols + col]) {
            ++cnt;
            visited[row*cols + col] = true;

            movingCore(threshold, rows, cols, row - 1, col, cnt, visited);
            movingCore(threshold, rows, cols, row, col - 1, cnt, visited);
            movingCore(threshold, rows, cols, row + 1, col, cnt, visited);
            movingCore(threshold, rows, cols, row, col + 1, cnt, visited);
        }
    }

    bool sumValid(int row, int col, int threshold) {
        int sumRow = 0, sumCol = 0;

        while(row) {
            sumRow += row % 10;
            row /= 10;
        }

        while(col) {
            sumCol += col % 10;
            col /= 10;
        }

        if(sumRow + sumCol <= threshold) {
            return true;
        }

        return false;
    }
};
```



#### 动态规划和贪心

##### 面试题14 剪绳子

【[OJ](https://www.nowcoder.com/practice/57d85990ba5b440ab888fc72b0751bf8?tpId=13&tqId=33257&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

```C++
class Solution {
public:
    // 动态规划
    int cutRope(int number) {
        if(number < 2)
            return 0;
        if(number == 2)
            return 1;
        if(number == 3)
            return 2;
        
        int *dp = new int[number + 1];
        dp[0] = 0;
        dp[1] = 1;
        dp[2] = 2;
        dp[3] = 3;
        
        int max = 0;
        
        for(int i = 4; i <= number; ++i) {
            max = 0;
            for(int j = 1; j <= i/2; ++j) {
                dp[i] = dp[j] * dp[i - j];
                if(dp[i] > max)
                    max = dp[i];
            }
        }
        
        max = dp[number];
        delete[] dp;
        
        return max;
    }
    
    // 贪心
    int cutRope_greedy(int number) {
        if(number < 2)
            return 0;
        if(number == 2)
            return 1;
        if(number == 3)
            return 2;
        
        int timesOf3 = number / 3;
        
        if(number - timesOf3 * 3 == 1)
            timesOf3 -= 1;
        
        int timesOf2 = (number - timesOf3 * 3) / 2;
        
        return (int)(pow(3, timesOf3)) * (int)(pow(2, timesOf2));
    }
};
```



#### 发散思维、抽象建模

##### 面试题61 扑克牌中的顺子（扑克牌顺子）

【[OJ](https://www.nowcoder.com/practice/762836f4d43d43ca9deb273b3de8e1f4?tpId=13&tqId=11198&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

先排序，再统计0的个数；求0后面各对数字的差，差以0补上，若0不够用则不是顺子；若一对数字相等，不是顺子。

```C++
class Solution {
public:
    bool IsContinuous( vector<int> numbers ) {
        if(numbers.empty())
            return false;
        
        sort(numbers.begin(), numbers.end()); // 排序
        
        int zeroCnt = 0;
        for(int number : numbers) { // 统计0的个数
            if(number == 0)
                zeroCnt++;
        }
        
        for(int i = zeroCnt; i < numbers.size(); ++i) {
            int diff = numbers[i+1] - numbers[i] - 1;
            if(diff == -1) { // 两数相等
                return false;
            } else{
                zeroCnt -= diff; // 使用0来填补空缺
                if(zeroCnt < 0) // 0不够用了
                    return false;
            }
        }
        
        return true;
    }
};
```



##### 面试题64 求1+2+3+...+n

【[OJ](https://www.nowcoder.com/practice/7a0da8fc483247ff8800059e12d7caf1?tpId=13&tqId=11200&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

使用递归解法最重要的是指定返回条件，但是本题无法直接使用 if 语句来指定返回条件。

求解方法：

1. 利用构造函数

2. 利用虚函数

3. 利用函数指针

4. 利用模板类型

5. 利用条件与&&的短路原则

   条件与 && 具有短路原则，即在第一个条件语句为 false 的情况下不会去执行第二个条件语句。利用这一特性，将递归的返回条件取非然后作为 && 的第一个条件语句，递归的主体转换为第二个条件语句，那么当递归的返回条件为 true 的情况下就不会执行递归的主体部分，递归返回。

   本题的递归返回条件为 n <= 0，取非后就是 n > 0；递归的主体部分为 sum += Sum_Solution(n - 1)，转换为条件语句后就是 (sum += Sum_Solution(n - 1)) > 0。

```C++
class Solution {
public:
    int Sum_Solution(int n) {
        int sum = n;
        sum && (sum += Sum_Solution(n - 1));
        return sum;
    }
};
```



##### 面试题65 不用加减乘除做加法

【[OJ](https://www.nowcoder.com/practice/59ac416b4b944300b617d4f7f111b215?tpId=13&tqId=11201&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)】

首先看十进制是如何做的： 5+7=12，三步走 

第一步：相加各位的值，不算进位，得到2。 

第二步：计算进位值，得到10. 如果这一步的进位值为0，那么第一步得到的值就是最终结果。 

第三步：重复上述两步，只是相加的值变成上述两步的得到的结果2和10，得到12。 

同样我们可以用三步走的方式计算二进制值相加： 5-101，7-111 

第一步：相加各位的值，不算进位，得到010，二进制每位相加就相当于各位做异或操作，101^111。

第二步：计算进位值，得到1010，相当于各位做与操作得到101，再向左移一位得到1010，(101&111)<<1。 

第三步重复上述两步， 各位相加 010^1010=1000，进位值为100=(010&1010)<<1。     继续重复上述两步：1000^100 = 1100，进位值为0，跳出循环，1100为最终结果。

```C++
class Solution {
public:
    int Add(int num1, int num2)
    {
        while (num2!=0) {
            int temp = num1^num2;
            num2 = (num1&num2) << 1;
            num1 = temp;
        }
        return num1;
    }
};
```

