# 字符串✏️🥇⭐️❌

# 1. 字符串循环移位包含

[编程之美 3.1](#)

```html
s1 = AABCD, s2 = CDAA
Return : true
```

给定两个字符串 s1 和 s2，要求判定 s2 是否能够被 s1 做循环移位得到的字符串包含。

s1 进行循环移位的结果是 s1s1 的子字符串，因此只要判断 s2 是否是 s1s1 的子字符串即可。



# 2. 字符串循环移位

[编程之美 2.17](#)

```html
s = "abcd123" k = 3
Return "123abcd"
```

将字符串向右循环移动 k 位。

将 abcd123 中的 abcd 和 123 单独翻转，得到 dcba321，然后对整个字符串进行翻转，得到 123abcd。



# 3. 字符串中单词的翻转

[程序员代码面试指南](#)

```html
s = "I am a student"
Return "student a am I"
```

将每个单词翻转，然后将整个字符串翻转。



# 4. 两个字符串包含的字符是否完全相同

242\. Valid Anagram / 有效的字母异位词 (Easy)

[Leetcode](https://leetcode.com/problems/valid-anagram/description/) / [力扣](https://leetcode-cn.com/problems/valid-anagram/description/)

```html
s = "anagram", t = "nagaram", return true.
s = "rat", t = "car", return false.
```

可以用 HashMap 来映射字符与出现次数，然后比较两个字符串出现的字符数量是否相同。

由于本题的字符串只包含 26 个小写字符，因此可以使用长度为 26 的整型数组对字符串出现的字符进行统计，不再使用 HashMap。

```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        vector<int> hash(26, 0);
        for(int i = 0; i < s.size(); i++)
            hash[s[i] - 'a']++;

        for(int i = 0; i < t.size(); i++)
            hash[t[i] - 'a']--;

        for(int i = 0; i < 26; i++) {
            if(hash[i] != 0)
                return false;
        }
        return true;
    }
};
```



# 5. 判断一个整数是否是回文数

9\. Palindrome Number (Easy)

[Leetcode](https://leetcode.com/problems/palindrome-number/description/) / [力扣](https://leetcode-cn.com/problems/palindrome-number/description/)

**题解**：

要求不能使用额外空间，也就不能将整数转换为字符串进行判断。

方法1：

首先判断x是否为负数，这里可以用一个小 trick，因为整数的最高位不能是0，所以回文数的最低位也不能为0，数字0除外，所以如果发现某个正数的末尾是0了，也直接返回 false 即可。

要验证回文数，那么就需**要看前后半段是否对称**，如果**把后半段翻转一下**，就看和前半段是否相等就行了。所以做法就是取出后半段数字，进行翻转，具体做法是，每次通过对 10 取余，取出最低位的数字，然后加到取出数的末尾，就是将 revertNum 乘以 10，再加上这个余数，这样翻转也就同时完成了，每取一个最低位数字，x都要自除以 10。这样当 revertNum 大于等于x的时候循环停止。由于回文数的位数可奇可偶，如果是**偶数**的话，那么 revertNum 就应该和x相等了；如果是**奇数**的话，那么最中间的数字就在 revertNum 的最低位上了，除以 10 以后应该和x是相等的

```cpp
class Solution {
public:
    bool isPalindrome(int x) {
        if(x < 0 || (x % 10 == 0 && x != 0)) return false; // 小于0或者最低位为0均不是回文
        int reverse = 0; // 翻转后半段
        while(x > reverse) { // x每次除以10，而reverse每次乘以10加x的余数，x小于等于reverse时应停止循环
            reverse = reverse * 10 + x % 10; // 将x的下一位放到reverse的低位
            x / 10;
        }
        return x == reverse || x == reverse / 10;
    }
};
```



方法2：将数字翻转后判断是否与原数字相等即可

```cpp
class Solution {
public:
    bool isPalindrome(int x) {
        if (x < 0 || (x % 10 == 0 && x != 0)) return false;
        return reverse(x) == x;
    }
    int reverse(int x) {
        int res = 0;
        while (x != 0) {
            if (res > INT_MAX / 10) return -1;
            res = res * 10 + x % 10;
            x /= 10;
        }
        return res;
    }
};
```



# 6. 回文字符串的最大长度

409\. Longest Palindrome / 最长回文串 (Easy)

[Leetcode](https://leetcode.com/problems/longest-palindrome/description/) / [力扣](https://leetcode-cn.com/problems/longest-palindrome/description/)

```html
Input : "abccccdd"
Output : 7
Explanation : One longest palindrome that can be built is "dccaccd", whose length is 7.
```

使用长度为 256 的整型数组来统计每个字符出现的个数，每个字符有偶数个可以用来构成回文字符串。

因为回文字符串最中间的那个字符可以单独出现，所以如果有单独的字符就把它放到最中间。



**题解**：

写法1：使用哈希表

```cpp
class Solution {
public:
    int longestPalindrome(string s) {
        unordered_map<char, int> hash;
        for(auto c : s) 
            hash[c]++;
        
        int cnt = 0; // 回文对数
        int flag = 0; // 是否有单个字符
        for(auto it : hash) {
            cnt += it.second / 2; // 统计回文对数
            if(!flag && (it.second % 2) == 1) // 是否有单个字符
                flag = 1;
        }
        return cnt*2 + flag; // 单个字符数量仅统计一次
    }
};
```



写法2：使用数组代替哈希表

```cpp
class Solution {
public:
    int longestPalindrome(string s) {
        vector<int> hash(256, 0);
        for(auto c : s)
            hash[c]++;
        
        int cnt = 0; // 回文对数
        for(auto it : hash)
            cnt += it / 2; // 统计回文对数

        return cnt*2 < s.size() ?  cnt*2 + 1 : cnt*2; // 有落单字符则长度+1
    }
};
```



# 7. 回文子字符串个数

647\. Palindromic Substrings (Medium)

[Leetcode](https://leetcode.com/problems/palindromic-substrings/description/) / [力扣](https://leetcode-cn.com/problems/palindromic-substrings/description/)

给定一个字符串，你的任务是计算这个字符串中有多少个回文子串。具有不同开始位置或结束位置的子串，即使是由相同的字符组成，也会被计为是不同的子串。

```html
Input: "aaa"
Output: 6
Explanation: Six palindromic strings: "a", "a", "a", "aa", "aa", "aaa".
```

**题解**：

解法比较粗暴，就是以字符串中的每一个字符都当作回文串中间的位置，然后向两边扩散，每当成功匹配两个左右两个字符，结果 res 自增1，然后再比较下一对。注意回文字符串有奇数和偶数两种形式，如果是奇数长度，那么i位置就是中间那个字符的位置，所以左右两遍都从i开始遍历；如果是偶数长度的，那么i是最中间两个字符的左边那个，右边那个就是 i+1。

```cpp
class Solution {
public:
    int res = 0;
    int countSubstrings(string s) {
        if(s.empty()) return 0;
        for(int i = 0; i < s.size(); i++) {
            helper(s, i, i); // 奇数长度
            helper(s, i, i + 1); // 偶数长度
        }
        return res;
    }

    void helper(string s, int i, int j) {
        while(i >= 0 && j < s.size() && s[i] == s[j]) {
            --i; ++j; ++res;
        }
    }
};
```



# 8. 字符串同构

205\. Isomorphic Strings (Easy)

[Leetcode](https://leetcode.com/problems/isomorphic-strings/description/) / [力扣](https://leetcode-cn.com/problems/isomorphic-strings/description/)

给定两个字符串 s 和 t，判断它们是否是同构的。如果 s 中的字符可以被替换得到 t ，那么这两个字符串是同构的。所有出现的字符都必须用另一个字符替换，同时保留字符的顺序。两个字符不能映射到同一个字符上，但字符可以映射自己本身。

```html
Given "egg", "add", return true.
Given "foo", "bar", return false.
Given "paper", "title", return true.
```

**题解**：

记录一个字符**上次出现的位置**，如果两个字符串中的字符上次出现的位置一样，那么就属于同构。例如 "foo" 和 "bar" 中 f 和 b 上次出现的位置为 0，"foo" 中第一个 o 和 "bar" 中 a 上次出现的位置也为 0（未更新），遍历到 "foo" 中第二个 o 和 "bar" 中 r时，o 出现的位置为 2 而 "bar" 中的 r 为 0 此时两字符出现的位置不同，因而两字符串不同构。即若同构，两字符串 "foo" 和 "bar" 中 2、3 位置的字符应该相同，而在 bar 中 2、3 位置的字符不同。

需要用两个 HashMap 分别来记录原字符串和目标字符串中字符出现情况，由于 ASCII 码只有 256 个字符，所以可以用一个 256 大小的数组来代替 HashMap，并初始化为0，遍历原字符串，分别从源字符串和目标字符串取出一个字符，然后分别在两个数组中查找其值，若不相等，则返回 false，若相等，将其值更新为 i + 1，因为默认的值是0，所以更新值为 i + 1，这样当 i=0 时，则映射为1，如果不加1的话，那么就无法区分是否更新了

```cpp
class Solution {
public:
    bool isIsomorphic(string s, string t) {
        int m[256] = {0}, n[256] = {0};
        for(int i = 0; i < s.size(); i++) {
            if(m[s[i]] != n[t[i]]) return false; // 两字符上次出现的位置一样
            m[s[i]] = i + 1; // 存储字符出现的位置，做加1处理
            n[t[i]] = i + 1; // 存储字符出现的位置，做加1处理
        }
        return true;
    }
};
```



# 9. 二进制字符串中连续 1 和连续 0 数量相同的子串个数

696\. Count Binary Substrings (Easy)

[Leetcode](https://leetcode.com/problems/count-binary-substrings/description/) / [力扣](https://leetcode-cn.com/problems/count-binary-substrings/description/)

```html
Input: "00110011"
Output: 6
Explanation: There are 6 substrings that have equal number of consecutive 1's and 0's: 
"0011", "01", "1100", "10", "0011", and "01".
```

**题解**：

直接用pre和cur两个变量，其中pre初始化为0，cur初始化为1，然后从第二个数字开始遍历，如果当前数字和前面的数字相同，则cur自增1，否则pre赋值为cur，cur重置1。然后判断如果pre大于等于cur，res自增1。

```cpp
class Solution {
public:
    int countBinarySubstrings(string s) {
        int res = 0, pre = 0, cur = 1;
        for (int i = 1; i < s.size(); ++i) {
            if (s[i] == s[i - 1]) ++cur;
            else {
                pre = cur;
                cur = 1;
            }
            if (pre >= cur) ++res;
        }
        return res;
    }
};
```
