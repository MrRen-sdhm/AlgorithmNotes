# 数学✏️🥇⭐️❌

# 素数分解

每一个数都可以分解成素数的乘积，例如 84 = 2<sup>2</sup> \* 3<sup>1</sup> \* 5<sup>0</sup> \* 7<sup>1</sup> \* 11<sup>0</sup> \* 13<sup>0</sup> \* 17<sup>0</sup> \* …



# 整除

令 x = 2<sup>m0</sup> \* 3<sup>m1</sup> \* 5<sup>m2</sup> \* 7<sup>m3</sup> \* 11<sup>m4</sup> \* …

令 y = 2<sup>n0</sup> \* 3<sup>n1</sup> \* 5<sup>n2</sup> \* 7<sup>n3</sup> \* 11<sup>n4</sup> \* …

如果 x 整除 y（y mod x == 0），则对于所有 i，mi <= ni。



# 最大公约数最小公倍数

x 和 y 的最大公约数为：gcd(x,y) =  2<sup>min(m0,n0)</sup> \* 3<sup>min(m1,n1)</sup> \* 5<sup>min(m2,n2)</sup> \* ...

x 和 y 的最小公倍数为：lcm(x,y) =  2<sup>max(m0,n0)</sup> \* 3<sup>max(m1,n1)</sup> \* 5<sup>max(m2,n2)</sup> \* ...



## 1. 生成素数序列

204\. Count Primes / 计数质数 (Easy)

求**小于**非负整数n的质数个数

[Leetcode](https://leetcode.com/problems/count-primes/description/) / [力扣](https://leetcode-cn.com/problems/count-primes/description/)

题解：

方法1：埃氏筛法

埃拉托斯特尼筛法在每次找到一个素数时，将能被素数整除的数排除掉。需要注意的是，此题求的是**小于**n的质数

![File:Sieve of Eratosthenes animation.gif](https://upload.wikimedia.org/wikipedia/commons/b/b9/Sieve_of_Eratosthenes_animation.gif)

```java
class Solution {
public:
    int countPrimes(int n) {
        int cnt = 0;
        vector<bool> st(n, false);

        for (int i = 2; i < n; i ++ ) { // <n
            if (st[i]) continue;
            cnt++;
            for (int j = i + i; j < n; j += i) // 筛掉倍数
                st[j] = true;
        }
        return cnt;
    }
};
```

有一点可以优化：j 从 i * i 开始筛，因为如果 k < i，那么 k * i 在之前就已经被筛过了

```cpp
class Solution {
public:
    int countPrimes(int n) {
        int cnt = 0;
        vector<bool> st(n, false);

        for (int i = 2; i < n; i ++ ) { // <n
            if (st[i]) continue;
            cnt++;
            // j 从 i * i 开始筛，因为如果 k < i，那么 k * i 在之前就已经被筛过了
            for (long j = (long)i * i; j < n; j += i) // 筛掉倍数
                st[j] = true;
        }
        return cnt;
    }
};
```

还有一点可以优化：就像素数判定的优化一样，外层循环中 i 可以只枚举到 sqrt(i)，但是**个数的统计需要单独循环**。

```cpp
class Solution {
public:
    int countPrimes(int n) {
        n -= 1; // 小于n的质数个数，转换为小于等于n-1的质数个数
        vector<bool> st(n, false);

        for (int i = 2; i <= n / i; i++) { // i*i < n
            if (st[i]) continue;
            for (long j = (long)i * i; j <= n; j += i) // 筛掉倍数
                st[j] = true;
        }

        int cnt = 0;
        for (int i = 2; i <= n; i++) // 统计质数个数
            if(!st[i]) cnt++;

        return cnt;
    }
};
```



方法2：线性筛法，更适用于求所有素数，而不仅仅是求素数个数

```cpp
class Solution {
public:
    int countPrimes(int n) {
        vector<int> primes(n, 0);
        vector<bool> st(n, false);
        int cnt = 0;

        for (int i = 2; i < n; i++) { // <n
            if (!st[i]) primes[cnt++] = i;
            for (int j = 0; primes[j] <= n / i; j++) {
                st[primes[j] * i] = true;
                if (i % primes[j] == 0) break; // primes[j]一定是i的最小质因子
            }
        }
        return cnt;
    }
};
```



## 2. 最大公约数

```java
int gcd(int a, int b) {
    return b == 0 ? a : gcd(b, a % b);
}
```

最小公倍数为两数的乘积除以最大公约数。

```java
int lcm(int a, int b) {
    return a * b / gcd(a, b);
}
```



## 3. 使用位操作和减法求解最大公约数

[编程之美：2.7](#)

对于 a 和 b 的最大公约数 f(a, b)，有：

- 如果 a 和 b 均为偶数，f(a, b) = 2\*f(a/2, b/2);
- 如果 a 是偶数 b 是奇数，f(a, b) = f(a/2, b);
- 如果 b 是偶数 a 是奇数，f(a, b) = f(a, b/2);
- 如果 a 和 b 均为奇数，f(a, b) = f(b, a-b);

乘 2 和除 2 都可以转换为移位操作。

```cpp
public int gcd(int a, int b) {
    if (a < b) return gcd(b, a);
    if (b == 0) return a;
    bool isAEven = isEven(a), isBEven = isEven(b);
    if (isAEven && isBEven) {
        return 2 * gcd(a >> 1, b >> 1);
    } else if (isAEven && !isBEven) {
        return gcd(a >> 1, b);
    } else if (!isAEven && isBEven) {
        return gcd(a, b >> 1);
    } else {
        return gcd(b, a - b);
    }
}
```



# 进制转换

<img src="https://gitee.com//MrRen-sdhm/Images/raw/master/img/20200731105030" alt="img" style="zoom: 50%;" />

## 1. 7 进制

504\. Base 7 (Easy)

[Leetcode](https://leetcode.com/problems/base-7/description/) / [力扣](https://leetcode-cn.com/problems/base-7/description/)

给定一个整数，将其转化为7进制，并以字符串形式输出。

```
输入: 101
输出: "203"
```

**题解**：

先用100除以7，商14余3，然后用14除以7，商2余0，再用2除以7，商0余2，得到203。这种方法更适合于代码实现，要注意的是，我们要处理好负数的情况。

方法1：迭代

```cpp
class Solution {
public:
    string convertToBase7(int num) {
        if(num == 0) return "0";
        bool positive = num > 0; // 记录num的正负号
        string res = "";
        while(num) {
            res = to_string(abs(num % 7)) + res; // 新计算出的余数放前面
            num /= 7; // 求商
        }
        return positive ? res : "-" + res; // 负数加上负号
    }
};
```



方法2：递归

```cpp
class Solution {
public:
    string convertToBase7(int num) {
        if (num < 0) return "-" + convertToBase7(-1 * num);
        if (num < 7) return to_string(num);
        return convertToBase7(num / 7) + to_string(num % 7);
    }
};
```



## 2. 16 进制

405\. Convert a Number to Hexadecimal (Easy)

[Leetcode](https://leetcode.com/problems/convert-a-number-to-hexadecimal/description/) / [力扣](https://leetcode-cn.com/problems/convert-a-number-to-hexadecimal/description/)

```html
Input:
26

Output:
"1a"

Input:
-1

Output:
"ffffffff"
```

**题解**：

采取位操作的思路，每次取出最右边四位，找到对应的字母加入结果，然后num像右平移四位，循环停止的条件是num为0，或者是已经循环了7次。负数要用它的补码形式。

```cpp
class Solution {
public:
    string toHex(int num) {
        string res = "", str = "0123456789abcdef";
        for(int i = 0; num != 0 && i < 8; i++) {
            res = str[(num & 0xf)] + res; // 取最右侧四位，得到对应的16进制数
            //printf("%d %c\n", num & 0xf, str[(num & 0xf)]);
            num >>= 4; // num右移四位，移除刚刚取出的四位
        }
        return res.empty() ? "0" : res;
    }
};
```



## 3. 26 进制

168\. Excel Sheet Column Title (Easy)

[Leetcode](https://leetcode.com/problems/excel-sheet-column-title/description/) / [力扣](https://leetcode-cn.com/problems/excel-sheet-column-title/description/)

给定一个正整数，返回它在 Excel 表中相对应的列名称。

```html
1 -> A
2 -> B
3 -> C
...
26 -> Z
27 -> AA
28 -> AB
```

**题解**：

因为是从 1 开始计算的，而不是从 0 开始，因此需要对 n 执行 -1 操作。

对于26来说，我们先让n自减1，变成25，然后再对26取余，得到25，此时再加上字符A，就可以得到字符Z了。这种方法对能被26整除的数和不能被26整除的数都成立。

写法1：迭代

```cpp
class Solution {
public:
    string convertToTitle(int n) {
        string res;
        while (n) {
            res = char(--n % 26 + 'A') + res; // n先自减1再求余，以处理被26整除的情况
            n /= 26; // 求商
        }
        return res; // 翻转
    }
};
```



写法2：递归

```cpp
class Solution {
public:
    string convertToTitle(int n) {
        return n == 0 ? "" : convertToTitle(n / 26) + (char)(--n % 26 + 'A');
    }
};
```



# 阶乘

## 1. 统计阶乘尾部有多少个 0

172\. Factorial Trailing Zeroes (Easy)

[Leetcode](https://leetcode.com/problems/factorial-trailing-zeroes/description/) / [力扣](https://leetcode-cn.com/problems/factorial-trailing-zeroes/description/)

```
输入: 3
输出: 0
解释: 3! = 6, 尾数中没有零。

输入: 5
输出: 1
解释: 5! = 120, 尾数中有 1 个零.
```

**题解**：

尾部的 0 由 2 * 5 得来，2 的数量明显多于 5 的数量（比如1到 10 中有2个5，5个2），因此只要统计有多少个 5 即可。

对于一个数 N，它所包含 5 的个数为：N/5 + N/5<sup>2</sup> + N/5<sup>3</sup> + ...，其中 N/5 表示不大于 N 的数中 5 的倍数贡献一个 5，N/5<sup>2</sup> 表示不大于 N 的数中 5<sup>2</sup> 的倍数再贡献一个 5 ...。

写法1：迭代

```cpp
class Solution {
public:
    int trailingZeroes(int n) {
        int res = 0;
        while(n) {
            res += n / 5;
            n /= 5;
        }
        return res;
    }
};
```



写法2：递归

```cpp
class Solution {
public:
    int trailingZeroes(int n) {
        return n == 0 ? 0 : n / 5 + trailingZeroes(n / 5);
    }
};
```



如果统计的是 N! 的二进制表示中最低位 1 的位置，只要统计有多少个 2 即可，该题目出自 [编程之美：2.2](#) 。和求解有多少个 5 一样，2 的个数为 N/2 + N/2<sup>2</sup> + N/2<sup>3</sup> + ...



# 字符串加法减法

## 1. 二进制加法

67\. Add Binary (Easy)

[Leetcode](https://leetcode.com/problems/add-binary/description/) / [力扣](https://leetcode-cn.com/problems/add-binary/description/)

```html
a = "11"
b = "1"
Return "100".
```

**题解**：

用了两个指针分别指向a和b的末尾，然后每次取出一个字符，转为数字，若无法取出字符则按0处理，然后定义进位carry，初始化为0，将三者加起来，对2取余即为当前位的数字，对2取商即为当前进位的值，记得最后还要判断下carry，如果为1的话，要在结果最前面加上一个1。

写法1：

```cpp
class Solution1 {
public:
    string addBinary(string a, string b) {
        string res = "";
        if(a.empty() || b.empty()) return res;
        int i = a.size() - 1, j = b.size() - 1, carry = 0; // 两指针分别指向两字符串的末尾
        while(i >= 0 || j >= 0) {
            int p = i >= 0 ? a[i--] - '0' : 0; // 若指针仍指向字符串内字符，计算字符对应的数字
            int q = j >= 0 ? b[j--] - '0' : 0; // 否则填补0
            int sum = p + q + carry; // 计算当前和
            res = to_string(sum % 2) + res; // 将余数补到现有字符串前面
            carry = sum / 2; // 计算进位
        }
        if(carry == 1) res = '1' + res; // 最后还要进位
        return res;
    }
};
```



写法2：简洁

```cpp
class Solution {
public:
    string addBinary(string a, string b) {
        string res = "";
        if(a.empty() || b.empty()) return res;
        int i = a.size() - 1, j = b.size() - 1, carry = 0; // 两指针分别指向两字符串的末尾
        while (carry == 1 || i >= 0 || j >= 0) { // 最后carry=1时再计算一次
            if (i >= 0 && a[i--] == '1') carry++; // 当前位为1进位加1
            if (j >= 0 && b[j--] == '1') carry++; // 指针超出字符串不做处理
            res = to_string(carry % 2) + res; // 将余数补到现有字符串前面
            carry /= 2; // 计算进位
        }
        return res;
    }
};
```



## 2. 字符串加法

415\. Add Strings (Easy)

[Leetcode](https://leetcode.com/problems/add-strings/description/) / [力扣](https://leetcode-cn.com/problems/add-strings/description/)

给定两个字符串形式的非负整数 `num1` 和`num2` ，计算它们的和。字符串的值为非负整数。

**题解**：和二进制字符串相加类似，一位一位相加，然后算和算进位，最后根据进位情况看需不需要补一个高位（有高位需多进行一次循环处理）

```cpp
class Solution {
public:
    string addStrings(string num1, string num2) {
        string res = "";
        if(num1.empty() || num2.empty()) return res;
        int i = num1.size() - 1, j = num2.size() - 1, carry = 0;
        while(i >= 0 || j >= 0 || carry == 1) {
            int p = i >= 0 ? num1[i--] - '0' : 0;
            int q = j >= 0 ? num2[j--] - '0' : 0;
            int sum = p + q + carry;
            res = to_string(sum % 10) + res;
            carry = sum / 10;
        }
        return res;
    }
};
```



# 相遇问题

## 1. 改变数组元素使所有的数组元素都相等⭐️

462\. Minimum Moves to Equal Array Elements II (Medium)

[Leetcode](https://leetcode.com/problems/minimum-moves-to-equal-array-elements-ii/description/) / [力扣](https://leetcode-cn.com/problems/minimum-moves-to-equal-array-elements-ii/description/)

```html
Input:
[1,2,3]

Output:
2

Explanation:
Only two moves are needed (remember each move increments or decrements one element):

[1,2,3]  =>  [2,2,3]  =>  [2,2,2]
```

每次可以对一个数组元素加一或者减一，求最小的改变次数。

**题解**：

这是个典型的相遇问题，移动距离最小的方式是所有元素都移动到**中位数**。

方法1：

先排序，时间复杂度O(NlogN)

设 m 为中位数。a 和 b 是 m 两边的两个元素，且 b > a。要使 a 和 b 相等，它们总共移动的次数为 b - a，这个值等于 (b - m) + (m - a)，也就是把这两个数移动到中位数的移动次数。

设数组长度为 N，则可以找到 N/2 对 a 和 b 的组合，使它们都移动到 m 的位置。

```cpp
class Solution {
public:
    int minMoves2(vector<int>& nums) {
        if(nums.empty()) return 0;
        sort(nums.begin(), nums.end()); // 排序
        int i = 0, j = nums.size() - 1, res = 0;
        while(i < j) res += nums[j--] - nums[i++]; // 统计各对元素的差值和
        return res;
    }
};
```



方法2：

排序后直接计算各数与中位数的差值，相加，时间复杂度O(NlogN)

```cpp
class Solution {
public:
    int minMoves2(vector<int>& nums) {
        if(nums.empty()) return 0;
        sort(nums.begin(), nums.end()); // 排序
        int mid = nums[nums.size() / 2], res = 0; // 取中位数 
        for(int num : nums) res += abs(num - mid); // 统计与中位数的差值和
        return res;
    }
};
```



方法3：

使用快速选择算法找中位数，时间复杂度 O(N)

```cpp
class Solution {
public:
    int minMoves2(vector<int>& nums) {
        if(nums.empty()) return 0;
        qselect(nums, 0, nums.size() - 1, nums.size() / 2 + 1); // 中位数是第len/2+1个数
        int mid = nums[nums.size() / 2], res = 0; // 第len/2+1个数在数组中的位置为len/2，数组下标从0开始
        for(int num : nums)  res += abs(num - mid); // 统计各数与中位数的差值和
        return res;
    }

    void qselect(vector<int>& q, int l, int r, int k) { // 快速选择，找出数组的第k个数，其下标为k-1
        if(l >= r) return;
        int i = l - 1, j = r + 1, x = q[l + r >> 1];
        while(i < j) {
            while(q[++i] < x);
            while(q[--j] > x);
            if(i < j) swap(q[i], q[j]);
        }
        if(j + 1 >= k) qselect(q, l, j, k);
        else qselect(q, j + 1, r, k);
    }
};
```



方法4：

使用STL库函数nth_element找到中位数

```cpp
class Solution {
public:
    int minMoves2(vector<int>& nums) {
        if(nums.empty()) return 0;
        int res = 0, mid = nums.size() / 2;
        nth_element(nums.begin(), nums.begin() + mid, nums.end()); // 类似快速选择
        for(int num : nums) res += abs(num - nums[mid]); // 统计与中位数的差值和
        return res;
    }
};
```



# 多数投票问题

## 1. 数组中出现次数多于 n / 2 的元素

169\. Majority Element /多数元素 (Easy)

[Leetcode](https://leetcode.com/problems/majority-element/description/) / [力扣](https://leetcode-cn.com/problems/majority-element/description/)

**题解**：

方法1：时间复杂度O(nlogn)，空间复杂度O(logn)，使用堆排空间复杂度可降到O(1)

先对数组排序，最中间那个数出现次数一定多于 n / 2。

```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        return nums[nums.size() / 2];
    }
};
```

方法2：

使用哈希表，时间复杂度O(n)，空间复杂度O(n)

```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        unordered_map<int, int> ump;
        for(int num :nums) {
            if(++ump[num] > nums.size() / 2) return num;
        }
        return -1;
    }
};
```

方法3：时间复杂度O(n)，空间复杂度O(1)

可以利用摩尔投票法来解决这个问题。可以这么理解该算法：使用 cnt 来统计一个元素出现的次数，当遍历到的元素和统计元素不相等时，令 cnt--。如果前面查找了 i 个元素，且 cnt == 0，说明前 i 个元素没有 majority，或者有 majority，但是出现的次数少于 i / 2，因为如果多于 i / 2 的话 cnt 就一定不会为 0。此时剩下的 n - i 个元素中，majority 的数目依然多于 (n - i) / 2，因此继续查找就能找出 majority。

总结：选一个数然后统计其出现的次数，规则是下一个数等于这个数则计数加1否则减1，次数减到0则换一个数

```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int cnt = 0, major;
        for(int num : nums) {
            if(cnt == 0) major = num;
            if(major == num) cnt++;
            else cnt--;
        }
        return major;
    }
};
```



# 其它

## 1. 完全平方数

367\. Valid Perfect Square (Easy)

[Leetcode](https://leetcode.com/problems/valid-perfect-square/description/) / [力扣](https://leetcode-cn.com/problems/valid-perfect-square/description/)

```html
Input: 16
Returns: True
```

**题解**：

方法1：

平方序列：0,1,4,9,16,..

间隔：1,3,5,7,...

间隔为等差数列，使用这个特性可以得到从 1 开始的平方序列。

```cpp
class Solution {
public:
    bool isPerfectSquare(int num) {
        int subnum = 1;
        while(num > 0) {
            num -= subnum;
            subnum += 2;
        }
        return num == 0;
    }
};
```



方法2：

从1搜索到 sqrt(num)，看有没有平方正好等于 num 的数，时间复杂度O(sqrt(n))

```cpp
class Solution {
public:
    bool isPerfectSquare(int num) {
        for (int i = 1; i <= num / i; ++i) {
            if (i * i == num) return true;
        }
        return false;
    }
};
```



方法3：

二分查找，时间复杂度O(log(n))

```cpp
class Solution {
public:
    bool isPerfectSquare(int num) {
        long long l = 0, r = num;
        while (l <= r) {
            long long mid = l + (r - l) / 2, t = mid * mid;
            if (t == num) return true;
            if (t > num) r = mid - 1;
            else l = mid + 1;
        }
        return false;
    }
};
```



## 2. 3 的 n 次方

326\. Power of Three (Easy)

[Leetcode](https://leetcode.com/problems/power-of-three/description/) / [力扣](https://leetcode-cn.com/problems/power-of-three/description/)

**题解**：

方法1：

由于输入是int，正数范围是0-231，在此范围中允许的最大的3的次方数为319=1162261467，那么我们只要看这个数能否被n整除即可

```cpp
class Solution {
public:
    bool isPowerOfThree(int n) {
        return n > 0 && (1162261467 % n == 0);
    }
};
```



方法2：

最直接的方法就是不停地除以3，看最后的迭代商是否为1，要注意考虑输入是负数和0的情况。

```c
class Solution {
public:
    bool isPowerOfThree(int n) {
        while(n && n % 3 == 0) n /= 3;
        return n == 1;
    }
};
```



## 3. 找出数组中的乘积最大的三个数

628\. Maximum Product of Three Numbers (Easy)

[Leetcode](https://leetcode.com/problems/maximum-product-of-three-numbers/description/) / [力扣](https://leetcode-cn.com/problems/maximum-product-of-three-numbers/description/)

```html
Input: [1,2,3,4]
Output: 24
```

**题解**：

方法1：

排序，时间复杂度O(nlogn)

- 如果全是正数，最后三个数相乘便是最大值。

- 如果全是负数，三个负数相乘还是负数，为了让负数最大，那么其绝对值就该最小，而负数排序后绝对值小的都在末尾，所以是末尾三个数字相乘，这个跟全是正数的情况一样。

- 那么重点在于前半段是负数，后半段是正数，那么最好的情况肯定是两个最小的负数相乘得到一个正数，然后跟一个最大的正数相乘，这样得到的肯定是最大的数，所以我们让**前两个数相乘，再和数组的最后一个数字相乘**，就可以得到这种情况下的最大的乘积。

实际上我们并不用分情况讨论数组的正负，只要把这两种情况的乘积都算出来，比较二者取较大值，就能涵盖所有的情况，从而得到正确的结果。

```cpp
class Solution {
public:
    int maximumProduct(vector<int>& nums) {
        if(nums.empty()) return 0;
        int n = nums.size();
        sort(nums.begin(), nums.end());
        int max1 = nums[0] * nums[1] * nums[n - 1];
        int max2 = nums[n - 1] * nums[n - 2] * nums[n - 3];
        return max(max1, max2);
    }
};
```



方法2：

找出3个最大的数 || 找出一个最大的和两个最小的，相乘对比也能得到结果，时间复杂度O(n)

```cpp
class Solution {
public:
    int maximumProduct(vector<int>& nums) {
        int mx1 = INT_MIN, mx2 = INT_MIN, mx3 = INT_MIN;
        int mn1 = INT_MAX, mn2 = INT_MAX;
        for (int num : nums) {
            if (num > mx1) {
                mx3 = mx2; mx2 = mx1; mx1 = num;
            } else if (num > mx2) {
                mx3 = mx2; mx2 = num;
            } else if (num > mx3) {
                mx3 = num;
            }
            if (num < mn1) {
                mn2 = mn1; mn1 = num;
            } else if (num < mn2) {
                mn2 = num;
            }
        }
        return max(mx1 * mx2 * mx3, mx1 * mn1 * mn2);
    }
};
```
