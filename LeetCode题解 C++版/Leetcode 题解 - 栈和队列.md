# 栈和队列✏️🥇⭐️❌

# 1. 用栈实现队列

232\. Implement Queue using Stacks  / 用栈实现队列 (Easy)

[Leetcode](https://leetcode.com/problems/implement-queue-using-stacks/description/) / [力扣](https://leetcode-cn.com/problems/implement-queue-using-stacks/description/)

**题解**：栈的顺序为后进先出，而队列的顺序为先进先出。使用两个栈实现队列，一个元素需要经过两个栈才能出队列，在经过第一个栈时元素顺序被反转，经过第二个栈时再次被反转，此时就是先进先出顺序。

 * 准备两个栈，push栈负责接收新元素，pop栈负责返回元素，若pop为空则取元素时将push中所有元素倒入pop
 * 关键点：只有pop栈为空时才能将push栈的元素倒入pop栈，并且必须一次倒完

```cpp
class MyQueue {
public:
    stack<int> stk, help;
    MyQueue() {

    }

    void push(int x) {
        stk.push(x);
    }

    int pop() {
        if(help.empty()) {
            while(!stk.empty()) {
                help.push(stk.top());
                stk.pop();
            }
        }
        
        if(help.empty()) throw "erro";
        
        int t = help.top();
        help.pop();
        return t;
    }

    int peek() {
        if(help.empty()) {
            while(!stk.empty()) {
                help.push(stk.top());
                stk.pop();
            }
        }
        
        if(help.empty()) throw "erro";
            
        return help.top();
    }

    bool empty() {
        return stk.empty() && help.empty();
    }
};
```



# 2. 用队列实现栈

225\. Implement Stack using Queues / 用队列实现栈 (Easy)

[Leetcode](https://leetcode.com/problems/implement-stack-using-queues/description/) / [力扣](https://leetcode-cn.com/problems/implement-stack-using-queues/description/)

**题解**：

方法1：使用一个队列

在将一个元素 x 插入队列时，为了维护原来的后进先出顺序，需要让 x 插入队列首部。而队列的默认插入顺序是队列尾部，因此在**将 x 插入队列尾部之后，需要让除了 x 之外的所有元素出队列，再入队列**，即通过pop操作保证后加入的元素处在队头位置。

```java
class MyStack {
public:
    queue<int> qu;
    MyStack() {

    }

    void push(int x) {
        qu.push(x);
        int cnt = qu.size();
        while(cnt-- > 1) { // 将队头的cnt-1个元素移到队尾
            qu.push(qu.front());
            qu.pop();
        }
    }

    int pop() {
        int t = qu.front();
        qu.pop();

        return t;
    }

    int top() {
        return qu.front();
    }

    bool empty() {
        return qu.empty();
    }
};
```

方法2：使用双栈和一个变量

使用topval变量保存栈顶，pop时将qu中除x外元素pop并push到辅助队列中，最后pop掉qu中的x但不加入辅助队列，此时通过交换还原pop掉x的qu队列。

```cpp
class MyStack {
public:
    queue<int> qu, help;
    int topval;
    MyStack() {

    }

    void push(int x) {
        qu.push(x);
        topval = x;
    }

    int pop() {
        while(qu.size() > 1) {
            topval = qu.front();
            help.push(qu.front());
            qu.pop();
        }

        int t = qu.front();
        qu.pop();

        swap(qu, help); // 交换

        return t;
    }

    int top() {
        return topval;
    }

    bool empty() {
        return qu.empty();
    }
};
```



# 3. 最小值栈

155\. Min Stack / 最小栈 (Easy)

[Leetcode](https://leetcode.com/problems/min-stack/description/) / [力扣](https://leetcode-cn.com/problems/min-stack/description/)

**题解**：元素直接压入主栈，仅当辅助栈为空或当前值小于等于辅助栈顶时，压入辅助栈。弹出时主栈栈顶元素等于辅助栈栈顶时，弹出辅助栈栈顶。

```java
class MinStack {
public:
    stack<int> stk, help;
    MinStack() {
        
    }
    
    void push(int x) {
        stk.push(x);
        if(help.empty() || x <= help.top()) help.push(x);
    }
    
    void pop() {
        if(stk.empty()) throw "stack is empty!";
        int t = stk.top();
        stk.pop();
        if(t == help.top())
            help.pop();
    }
    
    int top() {
        if(stk.empty()) throw "stack is empty!";
        return stk.top();
    }
    
    int getMin() {
        if(help.empty()) throw "help stack is empty!";
        return help.top();
    }
};
```

对于实现最小值队列问题，可以先将队列使用栈来实现，然后就将问题转换为最小值栈，这个问题出现在 编程之美：3.7。



# 4. 用栈实现括号匹配

20\. Valid Parentheses / 有效的括号 (Easy)

[Leetcode](https://leetcode.com/problems/valid-parentheses/description/) / [力扣](https://leetcode-cn.com/problems/valid-parentheses/description/)

```html
"()[]{}"

Output : true
```

**题解**：当前字符不是左括号时，判断是否与栈顶匹配，匹配则取出栈顶否则返回false。注意，不可省略`else return false; // 当前字符与栈顶不匹配，匹配失败`，用于判断`())))`这种情况，每个字符必须合法。

```cpp
class Solution {
public:
    bool isValid(string s) {
        if(s.empty()) return true;
        if(s.size() % 2 == 1) return false;

        stack<char> st;
        for(int i = 0; i < s.size(); i++) {
            if(s[i] == '(' || s[i] == '[' || s[i] == '{') // 将左括号加入栈中
                st.push(s[i]);
            else {
                if(!st.empty()) { // 当前字符与栈顶匹配则取出栈顶
                    if(s[i] == ')' && st.top() == '('||
                       s[i] == ']' && st.top() == '['||
                       s[i] == '}' && st.top() == '{')
                    	st.pop();
                    else return false; // 当前字符与栈顶不匹配，匹配失败
                } 
            }
        }
        return st.empty(); // 最后栈空则字符串有效
    }
};
```



# 单调栈

**单调栈定义**

- 单调栈是按单调性维护栈内元素的栈。分为单调递增栈和单调递减栈。

**单调栈适合的问题**

- 计算数组中每个数右边（或左边）第一个比它大（或小）的数，并计算二者之间的距离
- 寻找数组中的某个子数组，使得子数组中的最小值乘以子数组的长度最大
- 寻找数组中的某个子数组，使得子数组中最小值乘以子数组所有元素和最大

<img src="https://pic.leetcode-cn.com/90a071c6ff964fad556b7a28757d531288fcf5ea2fdbf8e2bdf0937f8a14f1fa-file_1560500620573" alt="ink-image" style="zoom: 20%;" />

下一个更大的数，[代码模板](https://labuladong.gitbook.io/algo/shu-ju-jie-gou-xi-lie/dan-tiao-zhan)：

```cpp
vector<int> nextGreaterElement(vector<int>& nums) {
    vector<int> ans(nums.size()); // 存放答案的数组
    stack<int> s;
    for (int i = nums.size() - 1; i >= 0; i--) { // 倒着往栈里放
        while (!s.empty() && s.top() <= nums[i]) { // 判定个子高矮
            s.pop(); // 矮个起开，反正也被挡着了。。。
        }
        ans[i] = s.empty() ? -1 : s.top(); // 这个元素身后的第一个高个
        s.push(nums[i]); // 入栈，接受之后的身高判定吧！
    }
    return ans;
}
```



## 1. 数组中元素与下一个比它大的元素之间的距离

739\. Daily Temperatures  / 每日温度 (Medium)

[Leetcode](https://leetcode.com/problems/daily-temperatures/description/) / [力扣](https://leetcode-cn.com/problems/daily-temperatures/description/)

```html
Input: [73, 74, 75, 71, 69, 72, 76, 73]
Output: [1, 1, 4, 2, 1, 1, 0, 0]
```

**题解**：此题需要借助**递减栈** ，即栈里只有递减元素。在遍历数组时用栈把数组中的数存起来（此题需要**存元素下标**），如果当前遍历的数比栈顶元素来的大，说明栈顶元素的下一个比它大的数就是当前元素。

具体操作：

遍历整个数组，若栈不空，且当前数字大于栈顶元素，那么若直接入栈的话就不是递减栈 ，所以需要取出栈顶元素，由于当前数字大于栈顶元素的数字，而且一定是第一个大于栈顶元素的数，直接求出下标差就是二者的距离。

继续看新的栈顶元素，直到当前数字小于等于栈顶元素停止，然后将数字入栈，这样就可以一直保持递减栈，且每个数字和第一个大于它的数的距离也可以算出来。

<img src="https://pic.leetcode-cn.com/7a133e857271e638c04b3a27c1eabc29570e585cc44d7da60eb039459a7f89cd-739.gif" alt="739.gif" style="zoom: 33%;" />

**拓展**：若要**前一个**比当前数大的数，则要顺序入栈



写法1：**通用写法**，逆序入栈

```cpp
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& T) {
        vector<int> res(T.size());
        stack<int> stk;
        for(int i = T.size() - 1; i >= 0; i--) { // 倒着往栈里放
            while(!stk.empty() && T[i] >= T[stk.top()]) stk.pop(); // 小的出栈
            res[i] = stk.empty() ? 0 : (stk.top() - i); // 栈中留下的即为大的
            stk.push(i); // 入栈等待判定
        }
        return res;
    }
};
```

写法2：顺序

```cpp
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& T) {
        vector<int> res(T.size(), 0);
        stack<int> stk;
        for(int i = 0; i < T.size(); i++) {
            while(!stk.empty() && T[i] > T[stk.top()]) { // 比栈顶大
                res[stk.top()] = i - stk.top(); // 计算距离
                stk.pop(); // 栈顶出栈
            }
            stk.push(i); // 入栈等待判定
        }
        return res;
    }
};
```



## 2. 数组1中元素在数组2中的下一个比它大的值

496\. Next Greater Element I / 下一个更大元素 I（Easy）

[力扣](https://leetcode-cn.com/problems/next-greater-element-i/)

给定两个没有重复元素的数组 nums1 和 nums2 ，其中nums1 是 nums2 的**子集**。找到 nums1 中每个元素在 nums2 中的下一个比其大的值。

```
输入: nums1 = [4,1,2], nums2 = [1,3,4,2].
输出: [-1,3,-1]
```

**题解**：nums1中的元素相当于键，可以忽略数组 nums1，先对将 nums2 中的每一个元素，求出其下一个更大的元素。随后对于将这些答案放入哈希表中，再遍历数组 nums1，并直接找出答案。

```cpp
class Solution {
public:
    vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {
        vector<int> res(nums1.size());
        stack<int> stk;
        unordered_map<int, int> mp;

        for(int i = nums2.size() - 1; i >= 0; i--) { // 倒着入栈
            while(!stk.empty() && stk.top() <= nums2[i]) stk.pop(); // 小的出栈
            mp[nums2[i]] = stk.empty() ? -1 : stk.top(); // 大的保存到哈希表
            stk.push(nums2[i]); // 入栈等待判定
        }

        for(int i = 0; i < nums1.size(); i++) { // 取哈希表中元素, 键为nums1中元素
            res[i] = mp[nums1[i]];
        }
        return res;
    }
};
```



## 3. 循环数组中比当前元素大的下一个元素

503\. Next Greater Element II / 下一个更大元素 II (Medium)

[Leetcode](https://leetcode.com/problems/next-greater-element-ii/description/) / [力扣](https://leetcode-cn.com/problems/next-greater-element-ii/description/)

```text
Input: [1,2,1]
Output: [2,-1,2]
Explanation: The first 1's next greater number is 2;
The number 2 can't find next greater number;
The second 1's next greater number needs to search circularly, which is also 2.
```

[**题解**](https://leetcode-cn.com/problems/next-greater-element-ii/solution/xia-yi-ge-geng-da-yuan-su-ii-by-leetcode/)：与 739. Daily Temperatures (Medium) 不同的是，数组是循环数组，并且最后要求的不是距离而是下一个元素。

写法1：通用写法

```cpp
class Solution {
public:
    vector<int> nextGreaterElements(vector<int>& nums) {
        int n = nums.size();
        vector<int> res(n);
        stack<int> stk;
        for(int i = 2*n - 1; i >= 0; i--) { // 倒着入栈，假装数组长度翻倍
            int num = nums[i % n]; // 获取实际值
            while(!stk.empty() && stk.top() <= num) stk.pop(); // 小的出栈
            res[i % n] = stk.empty() ? -1 : stk.top(); // 保存大的
            stk.push(num); // 入栈等待判定
        }
        return res;
    }
};
```

写法2：遍历两倍的数组，然后还是坐标 i 对 n 取余，取出数字，如果此时栈不为空，且栈顶元素小于当前数字，说明当前数字就是栈顶元素的右边第一个较大数，那么建立二者的映射，并且去除当前栈顶元素，最后如果 i 小于n，则把 i 压入栈。因为res的长度必须是 n，超过 n 的部分我们只是为了给之前栈中的数字找较大值，所以不能压入栈。

```cpp
class Solution {
public:
    vector<int> nextGreaterElements(vector<int>& nums) {
        int n = nums.size();
        vector<int> res(n, -1);
        stack<int> stk;
        for(int i = 0; i < n * 2; i++) { // 循环2次即可
            int num = nums[i % n]; // 循环数组下标转换为非循环下标
            while(!stk.empty() && num > nums[stk.top()]) {
                res[stk.top()] = num; // 保存下一个更大的数
                stk.pop();
            }
            if(i < n) // 仅第一次循环入栈，第二次只需寻找更大的数
                stk.push(i);
        }

        return res;
    }
};
```



# 单调队列

单调队列需要使用双端队列deque实现，也可以使用数组加双指针模拟双端队列，通常会使用双指针解决单调栈相关的问题。



## 1. 滑动窗口最大值

[Leetcode 239. 滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/)

**题解**：队列中存储元素下标，主要步骤：

- 判断队头是否已经滑出窗口：起点大于队头的值，说明队头已滑出窗口，弹出队头
- 判断队尾是否需要弹出：当前值比队尾值更小，弹出队尾

方法1：数组模拟双端队列

```cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        vector<int> res;
        if(nums.empty()) return res;

        int q[nums.size()]; // 队列
        int hh = 0, tt = -1; // 队头指针，队尾指针

        for(int i = 0; i < nums.size(); i++) {
            if(hh <= tt && i - k + 1 > q[hh]) hh++; // 起点大于队头的值，说明队头已滑出窗口，弹出队头
            while(hh <= tt && nums[i] >= nums[q[tt]]) tt--; // 当前值比队尾值更大，弹出队尾
            q[++tt] = i; // 插入当前值
            if(i >= k - 1) res.push_back(nums[q[hh]]); // 输出前k个数
        }
        return res;
    }
};
```

方法2：使用deque

```cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        vector<int> res;
        if(nums.empty()) return res;

        deque<int> dq;
        for(int i = 0; i < nums.size(); i++) {
            if(!dq.empty() && i - k + 1 > dq.front()) dq.pop_front(); // 起点大于队头，说明队头已滑出窗口
            while(!dq.empty() && nums[i] >= nums[dq.back()]) dq.pop_back(); // 当前值比队尾值大，弹出队尾
            dq.push_back(i); // 插入当前值
            if(i >= k - 1) res.push_back(nums[dq.front()]); // 输出前k个数
        }
        return res;
    }
};
```

