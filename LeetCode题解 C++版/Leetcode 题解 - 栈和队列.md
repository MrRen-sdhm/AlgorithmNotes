* [1. 用栈实现队列](#1-用栈实现队列)
* [2. 用队列实现栈](#2-用队列实现栈)
* [3. 最小值栈](#3-最小值栈)
* [4. 用栈实现括号匹配](#4-用栈实现括号匹配)
* [5. 数组中元素与下一个比它大的元素之间的距离](#5-数组中元素与下一个比它大的元素之间的距离)
* [6. 循环数组中比当前元素大的下一个元素](#6-循环数组中比当前元素大的下一个元素)



# 1. 用栈实现队列

232\. Implement Queue using Stacks  / 用栈实现队列 (Easy)

[Leetcode](https://leetcode.com/problems/implement-queue-using-stacks/description/) / [力扣](https://leetcode-cn.com/problems/implement-queue-using-stacks/description/)

**题解**：栈的顺序为后进先出，而队列的顺序为先进先出。使用两个栈实现队列，一个元素需要经过两个栈才能出队列，在经过第一个栈时元素顺序被反转，经过第二个栈时再次被反转，此时就是先进先出顺序。

 * 准备两个栈，push栈负责接收新元素，pop栈负责返回元素，若pop为空则取元素时将push中所有元素倒入pop
 * 关键点：只有pop栈为空时才能将push栈的元素倒入pop栈，并且必须一次倒完

```C++
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

```C++
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

```C++
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



# 5. 数组中元素与下一个比它大的元素之间的距离

739\. Daily Temperatures  / 每日温度 (Medium)

[Leetcode](https://leetcode.com/problems/daily-temperatures/description/) / [力扣](https://leetcode-cn.com/problems/daily-temperatures/description/)

```html
Input: [73, 74, 75, 71, 69, 72, 76, 73]
Output: [1, 1, 4, 2, 1, 1, 0, 0]
```

**题解**：在遍历数组时用栈把数组中的数存起来，如果当前遍历的数比栈顶元素来的大，说明栈顶元素的下一个比它大的数就是当前元素。

具体操作：

遍历整个数组，如果栈不空，且当前数字大于栈顶元素，那么如果直接入栈的话就不是 递减栈 ，所以需要取出栈顶元素，由于当前数字大于栈顶元素的数字，而且一定是第一个大于栈顶元素的数，直接求出下标差就是二者的距离。

继续看新的栈顶元素，直到当前数字小于等于栈顶元素停止，然后将数字入栈，这样就可以一直保持递减栈，且每个数字和第一个大于它的数的距离也可以算出来。

```java
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& T) {
        vector<int> res(T.size(), 0);
        stack<int> stk;
        for(int i = 0; i < T.size(); i++) {
            while(!stk.empty() && T[i] > T[stk.top()]) {
                int t = stk.top(); stk.pop();
                res[t] = i - t;
            }
            stk.push(i);
        }
        return res;
    }
};
```



# 6. 循环数组中比当前元素大的下一个元素

503\. Next Greater Element II / 下一个更大元素 II (Medium)

[Leetcode](https://leetcode.com/problems/next-greater-element-ii/description/) / [力扣](https://leetcode-cn.com/problems/next-greater-element-ii/description/)

```text
Input: [1,2,1]
Output: [2,-1,2]
Explanation: The first 1's next greater number is 2;
The number 2 can't find next greater number;
The second 1's next greater number needs to search circularly, which is also 2.
```

与 739. Daily Temperatures (Medium) 不同的是，数组是循环数组，并且最后要求的不是距离而是下一个元素。

```java
public int[] nextGreaterElements(int[] nums) {
    int n = nums.length;
    int[] next = new int[n];
    Arrays.fill(next, -1);
    Stack<Integer> pre = new Stack<>();
    for (int i = 0; i < n * 2; i++) {
        int num = nums[i % n];
        while (!pre.isEmpty() && nums[pre.peek()] < num) {
            next[pre.pop()] = num;
        }
        if (i < n){
            pre.push(i);
        }
    }
    return next;
}
```
