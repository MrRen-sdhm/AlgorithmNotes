# 搜索✏️🥇⭐️❌

# 总结

**1、深度优先搜索 DFS**

数据结构：使用stack

空间：O(n)​

不具有最短性

DFS中有回溯、剪枝

适用于对空间要求比较高，比较复杂的题

典型案例：[排列](#_5-排列)、[N皇后](#_3-N 皇后)

**2、宽度优先搜索 BFS**

- 数据结构：使用queue

- 空间：O(2<sup>n</sup>)，空间为指数级，比DFS大很多

- 具有最短性，用于求最短

- 基于迭代，不会爆栈，适用于层数较深的题

- 适用于找最短路径的题

  典型案例：[迷宫问题](#_1-迷宫问题) 、[二进制矩阵中的最短路径](#_2-二进制矩阵中的最短路径)

  代码框架：

```cpp
  void BFS()
  {
      queue<int> qu; // 定义队列
      vector<vector<int>> memo(m, vector<int>(n,0)); // 备忘录，用于记录已经访问的位置

      if(condition) return; // 判断边界条件，是否能直接返回结果

      qu.push(start); // 将起始位置加入到队列中
      memo[0][0] = 1; // 更新备忘录。

      while (!qu.empty()) {
          int cnt = qu.size(); // 获取当前队列中的节点个数
          while(cnt--) { // 遍历一层
              auto t = qu.front(); // 取队头
              qu.pop(); // 队头出队
              if(condition) { // 判断是否到达终点位置
                  // 某些输出或返回操作
              }
              // 根据某些条件获取下一层所有的节点
              if(condition) { // 条件判断，过滤掉不符合条件的位置
                  qu.push(); // 下一层节点入队
              }
          }
      }
  }
```

深度优先搜索和广度优先搜索广泛运用于树和图中，但是它们的应用远远不止如此。



# BFS

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/95903878-725b-4ed9-bded-bc4aae0792a9.jpg"/> </div>

广度优先搜索一层一层地进行遍历，每层遍历都是以上一层遍历的结果作为起点，遍历一个距离能访问到的所有节点。需要注意的是，遍历过的节点不能再次被遍历。

第一层：

- 0 -> {6,2,1,5}

第二层：

- 6 -> {4}
- 2 -> {}
- 1 -> {}
- 5 -> {3}

第三层：

- 4 -> {}
- 3 -> {}

每一层遍历的节点都与根节点距离相同。设 d<sub>i</sub> 表示第 i 个节点与根节点的距离，推导出一个结论：对于先遍历的节点 i 与后遍历的节点 j，有 d<sub>i</sub> <= d<sub>j</sub>。利用这个结论，可以求解最短路径等   **最优解**   问题：第一次遍历到目的节点，其所经过的路径为最短路径。应该注意的是，使用 BFS 只能求解无权图的最短路径，无权图是指从一个节点到另一个节点的代价都记为 1。

在程序实现 BFS 时需要考虑以下问题：

- 队列：用来存储每一轮遍历得到的节点；
- 标记：对于遍历过的节点，应该将它标记，防止重复遍历。



## 1、Flood FIill

### 1. 图像渲染

[Leetcode 733. Flood Fill / 图像渲染](https://leetcode-cn.com/problems/flood-fill/)

**题解**：此题可使用广度优先搜索也可以使用深度优先搜索，广度优先搜索的好处是不会爆栈

方法1：广度优先搜索，好处是不会爆栈。

```cpp
class Solution {
public:
    vector<vector<int>> floodFill(vector<vector<int>>& image, int sr, int sc, int newColor) {
        int m = image.size(), n = image[0].size();
        if(image[sr][sc] == newColor) // 新颜色与旧颜色相同
            return image;
        vector<vector<int>> memo(m, vector<int>(n, 0));
        queue<pair<int, int>> qu; // 像素队列
        int oldColor = image[sr][sc]; // 保存原始颜色

        qu.push({sr, sc}); // 起点加入队列
        image[sr][sc] = newColor; // 更新起点颜色
        memo[sr][sc] = 1; // 标记起点

        int dx[] = {0, 0, -1, 1}, dy[] = {1, -1 , 0, 0};
        while(!qu.empty()) {
            auto t = qu.front();
            qu.pop();

            for(int i = 0; i < 4; i++) {
                int x = t.first + dx[i], y = t.second + dy[i];
                if(x < 0 || x >= m || y < 0 || y >= n) continue;
                if(memo[x][y] == 1) continue; // 未标记
                if(image[x][y] == oldColor) { // 找到需要更新颜色的像素
                    image[x][y] = newColor; // 更新此像素颜色
                    qu.push({x, y}); // 新像素加入队列
                    memo[x][y] = 1; // 标记走过的像素点
                }
            }
        }
        return image;
    }
};
```



## 2、网格中原点到特定点的最短路径

### 1. 迷宫问题

【[nowcoder](https://www.nowcoder.com/questionTerminal/cf24906056f4488c9ddb132f317e03bc?orderByHotValue=0&commentTags=C/C++)】给定一个n\*m的二维整数数组，用来表示一个迷宫，数组中只包含0或1，其中0表示可以走的路，1表示不可通过的墙壁。最初，有一个人位于左上角(1, 1)处，已知该人每次可以向**上、下、左、右**任意一个方向移动一个位置。该人从左上角移动至右下角(n, m)处，输出其最短路径，或至少需要移动多少次。

PS：牛客的这道题，数据保证有唯一解，不考虑有多解的情况

```
输入样例：
5 5
0 1 0 0 0
0 1 0 1 0
0 0 0 0 0
0 1 1 1 0
0 0 0 1 0
输出样例：
8
```

此题求的是左上角到右下角的最短路径，比leetcode的迷宫问题要简单

**题解**：若题目输入保证有唯一解，则可使用回溯法解决，而通用的解法是使用广度优先搜索，使用广度优先搜索可方便求得最短路径的长度，但难点在于保存路径，属于**最短路模型**。

- 可从终点开始往起点搜索，这样既可方便的存储和输出从起点到终点的路径。

方法1：使用广度优先搜索，从终点往起点搜

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

int m, n;
vector<vector<int>> maze; // 迷宫
vector<vector<int>> dis; // 到起点的距离
queue<pair<int, int>> loc; // 坐标
vector<vector<pair<int, int>>> path;

int bfs(int sx, int sy) {
    loc.push({sx, sy}); // 初始化队列，放入起始坐标
    dis[sx][sy] = 0; // 标记起点距离
    
    int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};
    while(!loc.empty()) {
        auto t = loc.front();
        loc.pop();

        for(int i = 0; i < 4; i++) { // 四个方向
            int x = t.first + dx[i], y = t.second + dy[i];
            // 不是障碍物，并且没走过
            if(x < 0 || x >= m || y < 0 || y >= n) continue;
            if(maze[x][y] != 0) continue;
            if(dis[x][y] != -1) continue;
            
            dis[x][y] = dis[t.first][t.second] + 1; // 距离标记
            loc.push({x, y}); // 将可行坐标放入队列
            path[x][y] = t; // 记录之前走过的位置
        }
    }
    
    // 从终点（左上角点）开始找之前走过的路径
    pair<int, int> end(0, 0);
    while(true) {
        cout << '(' << end.first << ',' << end.second << ')' << endl;
        if(end.first == m - 1 && end.second == n - 1) break;
        end = path[end.first][end.second];
    }
    return dis[m - 1][n - 1];
}

int main() {
    while(cin >> m >> n) { // 处理多个case
        maze = vector<vector<int>>(m, vector<int>(n)); // 初始化迷宫
        dis = vector<vector<int>>(m, vector<int>(n, -1)); // 初始化距离标记
        path = vector<vector<pair<int, int>>>(m, vector<pair<int, int>>(n));
        for(int i= 0; i < m; i++){ // 输入迷宫
            for(int j = 0; j < n; j++) {
                cin >> maze[i][j];
                //cout << maze[i][j] << ' ';
            }
            //cout << endl;
        }
        int minDis = bfs(m - 1, n - 1); // 为便于输出路径，从终点开始搜索
        //cout << minDis <<　endl;
    }
    return 0;
} 
```

方法2：使用广度优先搜索，从起点往终点搜

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

int m, n;
vector<vector<int>> maze; // 迷宫
vector<vector<int>> dis; // 到起点的距离
queue<pair<int, int>> loc; // 坐标
vector<vector<pair<int, int>>> path;

int bfs() {
    loc.push({0, 0}); // 初始化队列，放入起始坐标
    dis[0][0] = 0; // 标记起点距离
    
    int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};
    while(!loc.empty()) {
        auto t = loc.front();
        loc.pop();

        for(int i = 0; i < 4; i++) { // 四个方向
            int x = t.first + dx[i], y = t.second + dy[i];
            // 不是障碍物，并且没走过
            if(x >= 0 && x < m && y >= 0 && y < n && maze[x][y] == 0 && dis[x][y] == -1){
                dis[x][y] = dis[t.first][t.second] + 1; // 距离标记
                loc.push({x, y}); // 将可行坐标放入队列
                path[x][y] = t; // 记录之前走过的位置
            }
        }
    }
    
    // 从终点向前找之前走过的路径
    int x = m - 1, y = n -1;
    vector<pair<int, int>> tpath;
    tpath.push_back({x, y}); // 保存终点位置
    while(x || y) { // x、y不越界
        auto t = path[x][y]; // 获取前一位置
        tpath.push_back(t); // 保存前一位置
        x = t.first, y = t.second;
    }
    for(int i = tpath.size() - 1; i >= 0; i--)
        cout << '(' << tpath[i].first << ',' << tpath[i].second << ')' << endl;
    
    return dis[m - 1][n - 1];
}

int main() {
    while(cin >> m >> n) { // 处理多个case
        maze = vector<vector<int>>(m, vector<int>(n)); // 初始化迷宫
        dis = vector<vector<int>>(m, vector<int>(n, -1)); // 初始化距离标记
        path = vector<vector<pair<int, int>>>(m, vector<pair<int, int>>(n));
        for(int i= 0; i < m; i++){ // 输入迷宫
            for(int j = 0; j < n; j++) {
                cin >> maze[i][j];
            }
        }
        int minDis = bfs();
    }
    return 0;
} 
```

经过的点和相对原点的距离：

```
(1,0)->1
(2,0)->2
(2,1)->3 (3,0)->3
(2,2)->4 (4,0)->4
(1,2)->5 (2,3)->5 (4,1)->5
(0,2)->6 (2,4)->6 (4,2)->6
(0,3)->7 (1,4)->7 (3,4)->7
(0,4)->8 (4,4)->8
```

距离相等的点处于一层，每次循环先取出队头，再将下一层的点加入队列



### 2. 二进制矩阵中的最短路径

1091\. Shortest Path in Binary Matrix / 二进制矩阵中的最短路径 (Medium)

[Leetcode](https://leetcode.com/problems/shortest-path-in-binary-matrix/) / [力扣](https://leetcode-cn.com/problems/shortest-path-in-binary-matrix/)

```html
[[1,1,0,1],
 [1,0,1,0],
 [1,1,1,1],
 [1,0,1,1]]
```

题目描述：0 表示可以经过某个位置，求解从左上角到右下角的最短路径长度，可向**八个方向**移动，路径不存在返回-1

**[题解](https://leetcode-cn.com/problems/shortest-path-in-binary-matrix/solution/biao-zhun-de-bfsjie-fa-duo-lian-xi-jiu-hui-zhang-w/)**：此题与上题的不同点在于可移动的方向多了四个，并且输入不保证路径存在，须判断路径不存在的情况，属于**最短路模型**。

方法1：标准BFS写法，每次处理一层，即距离起始点相等的一组点，使用int变量保存路径长度，使用备忘录保存已搜索的点

**注意：不可在循环中使用创建vector同于存储固定大小的经常访问的数组，会比使用原生数组耗时多很多！！！**

```java
class Solution {
public:
    int shortestPathBinaryMatrix(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        if(m == 0 || n == 0) return -1;

        int dis = 0;
        queue<pair<int, int>> loc;
        vector<vector<int>> memo(m, vector<int>(n,0)); // 备忘录
        // 初始化队列
        if(grid[0][0] != 0) return -1; // 判断起始点是否合法
        else loc.push({0, 0});
        memo[0][0] = 1; // 起始点加入备忘录

        while(!loc.empty()) {
            int cnt = loc.size();
            dis++; // 搜索完一层，距离加1
            while(cnt--) {
                auto t = loc.front();
                loc.pop(); // 取队头

                int cx = t.first, cy = t.second; // 当前行，当前列
                if(cx == m - 1 && cy == n - 1) // 已到达右下角
                    return dis;
                
                // 千万不可使用vector存储方向，会超时
                int dxy[8][2] = {{-1,-1},{-1,0},{-1,1},{0,-1},{0,1},{1,-1},{1,0},{1,1}};
                for(int i = 0; i < 8; i++) { // 尝试向8个方向移动
                    int x = cx + dxy[i][0], y = cy + dxy[i][1];
                    if(x >= 0 && x < m && y >= 0 && y < n && grid[x][y] == 0 && memo[x][y] != 1) {
                        memo[x][y] = 1; // 记录走过的点
                        loc.push({x, y}); // 添加有效路径点
                    }
                }
            }
        }
        return -1; // 未移动到右下角
    }
};
```


方法2：使用二维数组保存路径上各点离起点的距离，元素全部初始化为-1，可作为备忘录使用，未搜索的点距离值为-1

```cpp
class Solution1 {
public:
    int shortestPathBinaryMatrix(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        if(m == 0 || n == 0) return -1;

        vector<vector<int>> dis(m, vector<int>(n, -1));
        queue<pair<int, int>> loc;
        // 初始化队列
        if(grid[0][0] != 0) return -1; // 判断起始点是否合法
        loc.push({0, 0});
        dis[0][0] = 0; // 起始点路径长度为0
        while(!loc.empty()) {
            auto t = loc.front();
            loc.pop(); // 取队头

            // 千万不可使用vector存储方向，会超时
            int dx[8] = {-1,-1,-1,0,0,1,1,1}, dy[8] = {-1,0,1,-1,1,-1,0,1};
            int dxy[8][2] = {{-1,-1},{-1,0},{-1,1},{0,-1},{0,1},{1,-1},{1,0},{1,1}};
            for(int i = 0; i < 8; i++) { // 尝试向8个方向移动
                int x = t.first + dxy[i][0], y = t.second + dxy[i][1];
                if(x >= 0 && x < m && y >= 0 && y < n && grid[x][y] == 0 && dis[x][y] == -1) {
                    loc.push({x, y}); // 添加有效路径点
                    dis[x][y] = dis[t.first][t.second] + 1;
                }
            }
        }
        if(dis[m-1][n-1] == -1) // 未移动到右下角
            return -1;
        else
            return dis[m-1][n-1] + 1;
    }
};
```



## 3、组成整数的最小平方数数量

### 1. 完全平方数

279\. Perfect Squares / 完全平方数 (Medium)

[Leetcode](https://leetcode.com/problems/perfect-squares/description/) / [力扣](https://leetcode-cn.com/problems/perfect-squares/description/)

```html
For example, given n = 12, return 3 because 12 = 4 + 4 + 4; given n = 13, 
return 2 because 13 = 4 + 9.
```

**题解**：可以将每个整数看成图中的一个节点，如果两个整数之差为一个平方数，那么这两个整数所在的节点就有一条边。要求解最小的平方数数量，就是求解从节点 n 到节点 0 的最短路径。此题属于**最短路模型**。

第一层依次减去一个平方数得到第二层，第二层依次减去一个平方数得到第三层，以此类推，直到某一层出现0则当前层数即为所求。若某一层出现1，则下一层即为所求。

![](https://gitee.com//MrRen-sdhm/Images/raw/master/img/20200526231307.png)

下图为剪枝后的：

![](https://gitee.com//MrRen-sdhm/Images/raw/master/img/20200526231643.png)

本题也可以用动态规划求解，在之后动态规划部分中会再次出现。

```cpp
class Solution {
public:
    int numSquares(int n) {
        vector<int> dis(n+1, -1); // 存储距离，并作为备忘录
        queue<int> qu;

        qu.push(n); // 加入起点
        dis[n] = 0; // 记录起点距离

        while(!qu.empty()) {
            auto t = qu.front();
            qu.pop();
            if(t == 0) // 找到0，则当前距离即为最小步数
                return dis[0];

            for(int i = 1; t - i*i >= 0; i++) {
                int num = t - i*i; // 减去一个平方数

                if(dis[num] == -1) { // 未搜索到过
                    dis[num] = dis[t] + 1; // 距离加1
                    qu.push(num);
                }
            }
        }
        return -1; // 问题无解
    }
};
```



## 4、最短单词路径

### 1. 单词接龙

127\. Word Ladder / 单词接龙 (Medium)

[Leetcode](https://leetcode.com/problems/word-ladder/description/) / [力扣](https://leetcode-cn.com/problems/word-ladder/description/)

```html
Input:
beginWord = "hit",
endWord = "cog",
wordList = ["hot","dot","dog","lot","log","cog"]

Output: 5

Explanation: As one shortest transformation is "hit" -> "hot" -> "dot" -> "dog" -> "cog",
return its length 5.
```

```html
Input:
beginWord = "hit"
endWord = "cog"
wordList = ["hot","dot","dog","lot","log"]

Output: 0

Explanation: The endWord "cog" is not in wordList, therefore no possible transformation.
```

题目描述：找出一条从 beginWord 到 endWord 的**最短转换序列长度**，每次移动规定为改变一个字符，并且改变之后的字符串必须在 wordList 中。

**题解**：将单词看做点，如果两个单词可以相互转化，则在相应的点之间连一条无向边。那问题就变成了求从起点到终点的最短路。由于边权都相等，所以可以用BFS求最短路。此题属于**最小步数**模型。

- 此题使用普通的BFS会超时
- 优化：
  - 使用unordered_set将所有字符串保存到哈希表中，每次取出队头字符串时替换一个字符，并判断替换后的字符串是否在已存储的哈希表中
  - 使用双向BFS，从两个方向进行搜索，并保证每次添加新的节点到较短的队列中，使用两个哈希表分别存储两个方向搜索过的字符串。若将新节点加入较短队列的过程中，此节点已在较长队列中搜索过，则说明两个方向的搜索相遇，此时返回搜索的层数即可。


方法1：双向BFS，按层遍历，使用unordered_set存储备忘录（**通用双向BFS**）

```cpp
class Solution {
public:
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        bool hasEnd = false;
        for(auto word : wordList) {
            if(word == endWord) hasEnd = true;
        }
        if(!hasEnd) return 0;

        // BFS
        unordered_set<string> memoa, memob; // 备忘录
        queue<string> qua, qub;

        qua.push(beginWord), qub.push(endWord);
        memoa.insert(beginWord), memob.insert(endWord);

        int dis = 0;
        while(qua.size() && qub.size()) {
            dis++;
            if(qua.size() > qub.size()) { // 每次扩展较小的队列
                swap(qua, qub);
                swap(memoa, memob);
            }
            int cnt = qua.size();
            while(cnt--) {
                auto t = qua.front();
                qua.pop();
                
                for(auto word : wordList) {
                    if(memoa.count(word)) continue;
                    if(check(t, word)) { // word合法，可添加到队列
                        // 待扩展的word已搜索过，说明双向搜索相遇
                        if(memob.count(word)) // 必须在检查之后，保证word合法
                            return dis + 1;
                        
                        qua.push(word);
                        memoa.insert(word);
                    }
                }
            }
        }
        return 0;
    }

    bool check(string &s1, string &s2) {
        int cnt = 0;
        for(int i = 0; i < s1.size(); i++) {
            if(s1.at(i) != s2.at(i))
                if(++cnt > 1) return false; // 提前停止判断
        }
        return cnt == 1;
    }
};
```

方法2：双向BFS，按层遍历，使用unordered_set存储备忘录，优化字符串检查

```cpp
class Solution {
public:
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        bool hasEnd = false;
        for(auto word : wordList) {
            if(word == endWord) hasEnd = true;
        }
        if(!hasEnd) return 0;

        // BFS
        unordered_set<string> dict(wordList.begin(), wordList.end()); // 字典，存储所有字符串
        unordered_set<string> memoa, memob; // 备忘录
        queue<string> qua, qub;

        qua.push(beginWord), qub.push(endWord);
        memoa.insert(beginWord), memob.insert(endWord);

        int dis = 0;
        while(qua.size() && qub.size()) {
            dis++;
            if(qua.size() > qub.size()) { // 每次扩展较小的队列
                swap(qua, qub);
                swap(memoa, memob);
            }
            int cnt = qua.size();
            while(cnt--) {
                auto t = qua.front();
                qua.pop();
                
                for (int i = 0; i < t.size(); i++){
                    string word = t;
                    for (char c = 'a'; c <= 'z'; c++) {
                        word[i] = c; // 替换当前字符串的一个字符
                        if(!dict.count(word)) continue; // 字典中无此字符串，跳过
                        if(memoa.count(word)) continue; // 已搜过此字符串，跳过
                        // 待扩展的word已搜索过，说明双向搜索相遇
                        if(memob.count(word)) // 必须先确保word在字典中
                            return dis + 1;
                            
                        qua.push(word);
                        memoa.insert(word);
                    }
                }
            }
        }
        return 0;
    }
};
```

方法3：使用普通的BFS，未优化字符串检查，会超时（使用unordered_map保存距离，并作为备忘录），使用方法2的字符串检查优化即可通过所有测试样例

```cpp
class Solution {
public:
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        int n = wordList.size();
        bool hasEnd = false;
        for(auto word : wordList) {
            if(word == endWord) hasEnd = true;
        }
        if(!hasEnd) return 0;

        // BFS
        unordered_map<string, int> dis; // 保存距离，并作为备忘录
        queue<string> qu;

        qu.push(beginWord);
        dis[beginWord] = 1;

        while(!qu.empty()) {
            auto t = qu.front();
            qu.pop();

            if(t == endWord)
                return dis[t];
            
            for(auto word : wordList) {
                // if(dis.count(word)) continue; // 已使用此字符串，使用count不会插入此字符串，推荐使用
                if(check(t, word) && !dis[word]) { // 未使用此字符串，则dis[word]返回0并插入此字符串，不推荐使用
                    qu.push(word);
                    dis[word] = dis[t] + 1;
                }
            }
        }
        return 0;
    }

    int check(string s1, string s2) {
        int cnt = 0;
        for(int i = 0; i < s1.size(); i++) {
            if(s1[i] != s2[i]) cnt++;
        }
        return cnt == 1;
    }
};
```



## 5、多源BFS

### 1. 01矩阵

【[Letcode 542. 01 矩阵](https://leetcode-cn.com/problems/01-matrix/)】（Medium）

**题解**：题目给出了多个`1`，要找出每个`1`到`0`的最近曼哈顿距离。由于`1`到`0`的距离和`0`到`1`的距离一样的，所以可以换个思维：找出每个`0`到`1`的距离。题目可以抽象成多个起始点的BFS，首先把每个源点 0 入队，然后从各个 0 同时开始一圈一圈的向 1 扩散（每个 1 都是被离它最近的 0 扩散到的 ），扩散的时候可以设置二维数组dist来记录距离（即扩散的层次）并同时标志是否访问过。

```cpp
class Solution {
public:
    vector<vector<int>> updateMatrix(vector<vector<int>>& matrix) {
        int m = matrix.size(), n = matrix[0].size();
        queue<pair<int, int>> qu;
        vector<vector<int>> dis(m, vector<int>(n, -1));

        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(matrix[i][j] == 0) { // 将所有源点加入队列
                    qu.push({i, j});
                    dis[i][j] = 0;
                }
            }
        }

        int dx[] = {-1,0,1,0}, dy[] = {0,1,0,-1};
        while(!qu.empty()) {
            auto t = qu.front();
            qu.pop();

            for(int i = 0; i < 4; i++) {
                int x = t.first + dx[i], y = t.second + dy[i];
                if(x < 0 || x >= m || y <0 || y >= n) continue;
                if(dis[x][y] != -1) continue;

                qu.push({x, y});
                dis[x][y] = dis[t.first][t.second] + 1;
            }
        }
        return dis;
    }
};
```



# DFS

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/74dc31eb-6baa-47ea-ab1c-d27a0ca35093.png"/> </div><br>

广度优先搜索一层一层遍历，每一层得到的所有新节点，要用队列存储起来以备下一层遍历的时候再遍历。

而深度优先搜索在得到一个新节点时立即对新节点进行遍历：从节点 0 出发开始遍历，得到到新节点 6 时，立马对新节点 6 进行遍历，得到新节点 4；如此反复以这种方式遍历新节点，直到没有新节点了，此时返回。返回到根节点 0 的情况是，继续对根节点 0 进行遍历，得到新节点 2，然后继续以上步骤。

从一个节点出发，使用 DFS 对一个图进行遍历时，能够遍历到的节点都是从初始节点可达的，DFS 常用来求解这种   **可达性**   问题。

在程序实现 DFS 时需要考虑以下问题：

- 栈：用栈来保存当前节点信息，当遍历新节点返回时能够继续遍历当前节点。可以使用递归栈。
- 标记：和 BFS 一样同样需要对已经遍历过的节点进行标记。



## 1、二叉树

### 1. 二叉树的最大深度

[Leetcode 104. 二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)  给定一个二叉树，找出其最大深度。二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

给定二叉树 [3,9,20,null,null,15,7]，返回它的最大深度 3 。

```
    3
   / \
  9  20
    /  \
   15   7
```



**题解**：

方法1：标准DFS写法

```cpp
class Solution {
public:
    int res = 0;
    int maxDepth(TreeNode* root) {
        dfs(0, root);
        return res;
    }

    void dfs(int cnt, TreeNode* root) {
        if(!root) {
            res = max(res, cnt);
            return;
        }

        dfs(cnt + 1, root->left);
        dfs(cnt + 1, root->right);
    }
};
```

方法2：递归

```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(!root) return 0;
        int left = maxDepth(root->left);
        int right = maxDepth(root->right);
        return max(left, right) + 1;
    }
};
```



### 2. 输出二叉树从根到叶的路径

257\. Binary Tree Paths  / 二叉树的所有路径 (Easy)

[Leetcode](https://leetcode.com/problems/binary-tree-paths/description/) / [力扣](https://leetcode-cn.com/problems/binary-tree-paths/description/)

```html
  1
 /  \
2    3
 \
  5
```

```html
["1->2->5", "1->3"]
```

**题解**：使用深度优先遍历，每次遍历到一个节点就把该节点的值存入string中，然后判断是否为叶子节点，如果为叶子节点就需要把该条路路径添加进vector中。

- 本题需要注意string的用法，并且要注意对于string而言，采用**赋值传值**的方式（不是引用或者指针），这样就意味着每次调用这个函数就会创建一个新的string，并且还会在调用的时候把上一个string的值穿给他，这样就意味着每个函数中的path均不相同。每个dfs函数会继承一个新的字符串，即**每条路径会有一个独立的字符串，两者之间互不干扰**，若使用引用传递，则两条路径的内容均会加到同一字符串中。比如遍历1->2->5时，若使用引用传递，2和5就会加入同一字符串，进而右子树的路径中就会出现左子树的节点值。
- 对于vector采用引用传值的方式，因为只需要一个即可，并且需要每个函数操作的vector一致。
- 要注意此题在树的内部搜索，不需要恢复现场

```java
class Solution {
public:
    vector<string> res;
    vector<string> binaryTreePaths(TreeNode* root) {
        if(!root) return res;
        string path;
        dfs(root, path);
        return res;
    }

    void dfs(TreeNode* root, string path) { // 注意：string为值传递
        if(!root) return;

        path += to_string(root->val);   
        if(!root->left && !root->right) { // 叶子节点，保存路径
            res.push_back(path);
            return;
        } else { // 非叶子节点，继续搜
            path += "->";
            dfs(root->left, path);
            dfs(root->right, path);
        }
    }
};
```



## 2、连通性模型

连通性模型中不需要恢复现场，搜索全部在网格内部。

注意：连通性模型中**起点通常需要特殊处理**，即起点起始状态就是走过的，因此有两种处理方法：

- 在主函数写上 `memo[start_x][start_y] = true` 一开始就将起点标记为已走过状态，这样就可以像BFS一样在进入下一点时将memo置位
- 也可在dfs函数的开始写上 `memo[x][y] = true` 这样不仅可以将之后点的状态置1，也可以将起点的状态置1



### 1. 查找最大的连通面积

695\. Max Area of Island  / 岛屿的最大面积 (Medium)

[Leetcode](https://leetcode.com/problems/max-area-of-island/description/) / [力扣](https://leetcode-cn.com/problems/max-area-of-island/description/)

```html
[[0,0,1,0,0,0,0,1,0,0,0,0,0],
 [0,0,0,0,0,0,0,1,1,1,0,0,0],
 [0,1,1,0,1,0,0,0,0,0,0,0,0],
 [0,1,0,0,1,1,0,0,1,0,1,0,0],
 [0,1,0,0,1,1,0,0,1,1,1,0,0],
 [0,0,0,0,0,0,0,0,0,0,1,0,0],
 [0,0,0,0,0,0,0,1,1,1,0,0,0],
 [0,0,0,0,0,0,0,1,1,0,0,0,0]]
```

**题解**：连通性模型，求的是最大连通面积，使用BFS、DFS均可

```cpp
// bfs
class Solution1 {
public:
    int maxAreaOfIsland(vector<vector<int>>& grid) {
        if(grid.empty()) retrun 0; // 输入检查
        int m = grid.size(), n = grid[0].size();
        vector<vector<bool>> memo(m, vector<bool>(n, 0));

        int res = 0;
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(grid[i][j] == 1 && memo[i][j] != 1) {
                    int cnt = bfs(i, j, m, n, memo, grid);
                    res = max(cnt, res);
                }
            }
        }
        return res;
    }

    int bfs(int x, int y, int m, int n, vector<vector<bool>>& memo, vector<vector<int>>& grid) {
        queue<pair<int, int>> qu;
        qu.push({x, y});
        memo[x][y] = 1;

        int cnt = 1;
        int dx[] = {-1,0,1,0}, dy[] = {0,1,0,-1};
        while(!qu.empty()) {
            auto t = qu.front(); qu.pop();

            for(int i = 0; i < 4; i++) {
                int a = t.first + dx[i], b = t.second + dy[i];
                if(a < 0 || a >= m || b < 0 || b >= n) continue;
                if(memo[a][b]) continue;
                if(grid[a][b] == 0) continue;

                qu.push({a, b});
                memo[a][b] = 1;
                cnt++;
            }
        }
        return cnt;
    }
};

// dfs
class Solution {
public:
    int maxAreaOfIsland(vector<vector<int>>& grid) {
        if(grid.empty()) retrun 0; // 输入检查
        int m = grid.size(), n = grid[0].size();
        vector<vector<bool>> memo(m, vector<bool>(n, 0));

        int res = 0;
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(grid[i][j] == 1 && memo[i][j] != 1) {
                    int cnt = dfs(i, j, m, n, memo, grid);
                    res = max(cnt, res);
                }
            }
        }
        return res;
    }

    int dfs(int x, int y, int m, int n, vector<vector<bool>>& memo, vector<vector<int>>& grid) {
        if(grid[x][y] == 0) return 0;
        int cnt = 1;
        memo[x][y] = 1;

        int dx[] = {-1,0,1,0}, dy[] = {0,1,0,-1};
        for(int i = 0; i < 4; i++) {
            int a = x + dx[i], b = y + dy[i];
            if(a < 0 || a >= m || b < 0 || b >= n) continue;
            if(memo[a][b]) continue;
            if(grid[a][b] == 0) continue;

            cnt += dfs(a, b, m, n, memo, grid);
        }        
        return cnt;
    }
};
```



### 2. 矩阵中的连通分量数目

200\. Number of Islands  / 岛屿数量 (Medium)

[Leetcode](https://leetcode.com/problems/number-of-islands/description/) / [力扣](https://leetcode-cn.com/problems/number-of-islands/description/)

```html
Input:
11000
11000
00100
00011

Output: 3
```

**题解**：和上题区别不大，每次填充一块连通区域，计数加1。可将原网格作为备忘录。

```cpp
// dfs，自建备忘录
class Solution1 {
public:
    int numIslands(vector<vector<char>>& grid) {
        if(grid.empty()) return 0; // 输入检查
        int m = grid.size(), n = grid[0].size();
        vector<vector<bool>> memo(m, vector<bool>(n, 0));

        int res = 0;
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(grid[i][j] == '1' && memo[i][j] != 1) {
                    dfs(i, j, m, n, memo, grid); // dfs会标记搜过的点
                    res++; // 找到一个可搜的点，连通区域个数即加1
                }
            }
        }
        return res;
    }

    void dfs(int x, int y, int m, int n, vector<vector<bool>>& memo, vector<vector<char>>& grid) {
        if(grid[x][y] == '0') return;
        memo[x][y] = 1;

        int dx[] = {-1,0,1,0}, dy[] = {0,1,0,-1};
        for(int i = 0; i < 4; i++) {
            int a = x + dx[i], b = y + dy[i];
            if(a < 0 || a >= m || b < 0 || b >= n) continue;
            if(memo[a][b]) continue;
            //if(grid[a][b] == '0') continue;

            dfs(a, b, m, n, memo, grid);
        }        
        return;
    }
};

// dfs，将grid作为备忘录
class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        if(grid.empty()) return 0; // 输入检查
        int m = grid.size(), n = grid[0].size();

        int res = 0;
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(grid[i][j] == '1') {
                    dfs(i, j, m, n, grid); // dfs会标记搜过的点
                    res++; // 找到一个可搜的点，连通区域个数即加1
                }
            }
        }
        return res;
    }

    void dfs(int x, int y, int m, int n, vector<vector<char>>& grid) {
        if(grid[x][y] == '0') return;
        grid[x][y] = '0';

        int dx[] = {-1,0,1,0}, dy[] = {0,1,0,-1};
        for(int i = 0; i < 4; i++) {
            int a = x + dx[i], b = y + dy[i];
            if(a < 0 || a >= m || b < 0 || b >= n) continue;
            //if(grid[a][b] == '0') continue;

            dfs(a, b, m, n, grid);
        }        
        return;
    }
};
```



### 3. 好友关系的连通分量数目

547\. Friend Circles / 朋友圈 (Medium)

[Leetcode](https://leetcode.com/problems/friend-circles/description/) / [力扣](https://leetcode-cn.com/problems/friend-circles/description/)

```html
Input:
[[1,1,0],
 [1,1,0],
 [0,0,1]]

Output: 2

Explanation:The 0th and 1st students are direct friends, so they are in a friend circle.
The 2nd student himself is in a friend circle. So return 2.
```

题目描述：好友关系可以看成是一个无向图，例如第 0 个人与第 1 个人是好友，那么 `M[0][1]` 和 `M[1][0]` 的值都为 1。此题并非二维网格，实际上每行代表一个人，其内容表示与其他人的关系，即每行代表图中的一个节点，因而是**一维网格**中的搜索问题。

```java
class Solution {
public:
    int findCircleNum(vector<vector<int>>& M) {
        if(M.empty()) return 0; // 输入检查
        int m = M.size();
        vector<int> memo(m, 0);

        int res = 0;
        for(int i = 0; i < m; i++) {
            if(memo[i] != 1) {
                dfs(i, m, memo, M); // dfs会标记搜过的人
                res++; // 找到一个可搜的人，朋友圈个数即加1
            }
        }
        return res;
    }

    void dfs(int x, int m, vector<int>& memo, vector<vector<int>>& M) {
        memo[x] = 1;

        for(int i = 0; i < m; i++) { // 遍历每个人，加入有联系的人
            if(memo[i]) continue; // 已搜过
            if(M[i][x] == 0) continue; // 无联系
            
            dfs(i, m, memo, M);
        }
        return;
    }
};
```



### 4. 填充封闭区域

130\. Surrounded Regions / 被围绕的区域 (Medium)

[Leetcode](https://leetcode.com/problems/surrounded-regions/description/) / [力扣](https://leetcode-cn.com/problems/surrounded-regions/description/)

```html
For example,
X X X X
X O O X
X X O X
X O X X

After running your function, the board should be:
X X X X
X X X X
X X X X
X O X X
```

题目描述：使被 'X' 包围的 'O' 转换为 'X'。

**题解**：先填充最外侧，剩下的就是里侧了。

方法1：稍繁琐，第一次dfs从四周开始搜O，并标记连通的O；第二次dfs根据标记在中间区域开始搜O，并覆盖O。使用flag标识dfs是否覆盖原board中的O。

```cpp
class Solution {
public:
    int m, n;   
    void solve(vector<vector<char>>& board) {
        if(board.empty() || board[0].empty()) return;
        m = board.size(), n = board[0].size();
        vector<vector<int>> memo(m, vector<int>(n, 0));

        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(board[i][j] == 'O') {
                    if(i == 0 || j == 0 || i == m - 1 || j == n - 1) // 标记与四周的O连通的O
                        dfs(i, j, false, memo, board);
                }
            }
        }

        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(board[i][j] == 'O') {
                    if(i == 0 || j == 0 || i == m - 1 || j == n - 1) continue;
                    if(!memo[i][j]) dfs(i, j, true, memo, board); // 从中间开始搜O并填充连通区域
                }
            }
        }
    }

    void dfs(int x, int y, bool flag, vector<vector<int>> &memo, vector<vector<char>>& board) {
        if(board[x][y] == 'X') return;
        memo[x][y] = 1;
        if(flag) board[x][y] = 'X';

        int dx[] = {-1,0,1,0}, dy[] = {0,1,0,-1};
        for(int i = 0; i < 4; i++) {
            int a = x + dx[i], b = y + dy[i];
            if(a < 0 || a >= m || b < 0 || b >= n) continue;
            if(memo[a][b]) continue;
            // if(board[a][b] == 'X') continue;

            dfs(a, b, flag, memo, board);
        }
        return;
    }
};
```

方法2：其实只需要方法1中的第1次dfs，即标记与四周O连通的O，不需要进行第二次dfs。直接遍历数组，将第一次未标记的O覆盖为X即可，同时省了覆盖标志。代码中重开了数组作为备忘录，当然也可以直接将board中与四周O连通的O标记为其他符号，从而省去备忘录数组。

```cpp
class Solution {
public:
    int m, n;   
    void solve(vector<vector<char>>& board) {
        if(board.empty() || board[0].empty()) return;
        m = board.size(), n = board[0].size();
        vector<vector<int>> memo(m, vector<int>(n, 0));

        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(board[i][j] == 'O') {
                    if(i == 0 || j == 0 || i == m - 1 || j == n - 1) // 标记与四周的O连通的O
                        dfs(i, j, memo, board);
                }
            }
        }

        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(board[i][j] == 'O' && !memo[i][j]) { // 不与四周O连通的O覆盖为X
                    board[i][j] = 'X';
                }
            }
        }
    }

    void dfs(int x, int y, vector<vector<int>> &memo, vector<vector<char>>& board) {
        if(board[x][y] == 'X') return;
        memo[x][y] = 1;

        int dx[] = {-1,0,1,0}, dy[] = {0,1,0,-1};
        for(int i = 0; i < 4; i++) {
            int a = x + dx[i], b = y + dy[i];
            if(a < 0 || a >= m || b < 0 || b >= n) continue;
            if(memo[a][b]) continue;
            // if(board[a][b] == 'X') continue;

            dfs(a, b, memo, board);
        }
        return;
    }
};
```

两种方法思路不同，方法二更直接些，但两者效率相差不大



### 5. 能到达的太平洋和大西洋的区域

417\. Pacific Atlantic Water Flow / 太平洋大西洋水流问题 (Medium)

[Leetcode](https://leetcode.com/problems/pacific-atlantic-water-flow/description/) / [力扣](https://leetcode-cn.com/problems/pacific-atlantic-water-flow/description/)

```html
Given the following 5x5 matrix:

  Pacific ~   ~   ~   ~   ~
       ~  1   2   2   3  (5) *
       ~  3   2   3  (4) (4) *
       ~  2   4  (5)  3   1  *
       ~ (6) (7)  1   4   5  *
       ~ (5)  1   1   2   4  *
          *   *   *   *   * Atlantic

Return:
[[0, 4], [1, 3], [1, 4], [2, 2], [3, 0], [3, 1], [4, 0]] (positions with parentheses in above matrix).
```

左边和上边是太平洋，右边和下边是大西洋，内部的数字代表海拔，海拔高的地方的水能够流到低的地方，求解水能够流到太平洋和大西洋的所有位置。

**题解**：

方法1：将矩阵中的每个点作为起点，搜索附近较低或等高的点，设置两个全局标志位分别标记此点能否到达太平洋和大西洋。此方法超时，每个点均需进行深度优先搜索。

```java
class Solution {
public:
    int m, n;
    bool flag1 = false, flag2 = false;
    vector<vector<int>> pacificAtlantic(vector<vector<int>>& matrix) {
        if(matrix.empty() || matrix[0].empty()) return {};
        m = matrix.size(), n = matrix[0].size();
        vector<vector<int>> memo(m, vector<int>(n, 0));
        vector<vector<int>> res;

        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                flag1 = false, flag2 = false;
                memo = vector<vector<int>>(m, vector<int>(n, 0));
                dfs(i, j, memo, matrix);
                if(flag1 && flag2) {
                    vector<int> vec = {i, j};
                    res.push_back(vec);
                }
            }
        }
        return res;
    }

    void dfs(int x, int y, vector<vector<int>>& memo, vector<vector<int>>& matrix) {
        if(x == 0 || y == 0) flag1 = true;
        if(x == m - 1 || y == n - 1) flag2 = true;
        memo[x][y] = 1;

        int dx[] = {-1,0,1,0}, dy[] = {0,1,0,-1};
        for(int i = 0; i < 4; i++) {
            int a = x + dx[i], b = y + dy[i];
            if(a < 0 || a >= m || b < 0 || b >= n) continue;
            if(matrix[a][b] > matrix[x][y]) continue;
            if(memo[a][b]) continue;

            dfs(a, b, memo, matrix);
        }
        return;
    }
};
```

方法2：那么可以从太平洋和大西洋的入口往回搜，标记比入口高的或等高的点即为可到达入口的点，设置两个二维数组分别存储能够到达太平洋和大西洋点，再同时遍历两个数组，找到同时能够到达太平洋和大西洋的点即可。

```cpp
class Solution {
public:
    int m, n;
    vector<vector<int>> pacificAtlantic(vector<vector<int>>& matrix) {
        if(matrix.empty() || matrix[0].empty()) return {};
        m = matrix.size(), n = matrix[0].size();
        vector<vector<int>> canReachP(m, vector<int>(n, 0)); // 记录与太平洋连通的点
        vector<vector<int>> canReachA(m, vector<int>(n, 0)); // 记录与大西洋连通的点
        vector<vector<int>> res;

        for(int i = 0; i < m; i++) {
            dfs(i, 0, canReachP, matrix); // dfs搜索与第一列连通的点
            dfs(i, n - 1, canReachA, matrix); // dfs搜索与最后一列连通的点
        }

        for(int i = 0; i < n; i++) {
            dfs(0, i, canReachP, matrix); // dfs搜索与第一行连通的点
            dfs(m - 1, i, canReachA, matrix); // dfs搜索与最后一行连通的点
        }

        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(canReachP[i][j] && canReachA[i][j]) {
                    vector<int> vec = {i, j};
                    res.push_back(vec);
                }
            }
        }
        return res;
    }

    void dfs(int x, int y, vector<vector<int>>& canReach, vector<vector<int>>& matrix) {
        canReach[x][y] = 1;

        int dx[] = {-1,0,1,0}, dy[] = {0,1,0,-1};
        for(int i = 0; i < 4; i++) {
            int a = x + dx[i], b = y + dy[i];
            if(a < 0 || a >= m || b < 0 || b >= n) continue;
            if(matrix[a][b] < matrix[x][y]) continue; // 跳过比当前点低的点
            if(canReach[a][b]) continue;

            dfs(a, b, canReach, matrix);
        }
        return;
    }
};
```



## 3、回溯

Backtracking（回溯）属于 DFS。

- 普通 DFS 主要用在 **可达性问题** ，这种问题只需要执行到特点的位置然后返回即可。
- 而 Backtracking 主要用于求解 **排列组合** 问题，例如有 { 'a','b','c' } 三个字符，求解所有由这三个字符排列得到的字符串，这种问题在执行到特定的位置返回之后还会继续执行求解过程。

因为 Backtracking 不是立即返回，而要继续求解，因此在程序实现时，需要注意对元素的标记问题：

- 在访问一个新元素进入新的递归调用时，需要将新元素标记为已经访问，这样才能在继续递归调用时不用重复访问该元素；
- 但是在递归返回时，需要将元素标记为未访问，因为只需要保证在一个递归链中不同时访问一个元素，可以访问已经访问过但是不在当前递归链中的元素。

### 一、字符串类型

### 1. 数字键盘组合

17\. Letter Combinations of a Phone Number / 电话号码的字母组合 (Medium)

[Leetcode](https://leetcode.com/problems/letter-combinations-of-a-phone-number/description/) / [力扣](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/description/)

<img src="https://assets.leetcode-cn.com/aliyun-lc-upload/original_images/17_telephone_keypad.png" alt="img" style="zoom:50%;" />

```html
Input:Digit string "23"
Output: ["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
```

**题解**：此题与[小猫爬山问题](https://www.acwing.com/problem/content/167/)的思想非常接近，需要安排的位数是已知的，若输入n个字符则各输出的字符数也为n。思路就是每次安排一个位置，但不同的是这里安排每个位置时的可选择列表是变化的，每个位置的可选择列表由输入的字符决定。如样例中，第一个位置可选择列表为['a', 'b', 'c']，第二个位置可选择列表为['d', 'e', 'f']，搜索不同的位置时切换不同的选择列表即可。

![img](https://pic.leetcode-cn.com/0ac574ab37f620221e702f57d6c4ffd0ba246abe41c43f9fc9637ab8f3365377-image.png)

```java
class Solution {
public:
    vector<string> res;
    vector<string> dict = {"abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"};

    vector<string> letterCombinations(string digits) {
        if(digits.empty()) return res;
        string path;
        dfs(0, path, digits); // 安排第0号位

        return res;
    }

    void dfs(int u, string& path, string& digits) {
        if(u == digits.size()) { // 已安排最后一个位置
            res.push_back(path);
            return;
        }

        string str = dict[digits[u] - '2']; // 此位置可选的字符序列
        for(int i = 0; i < str.size(); i++) {
            path += str[i]; // 选择
            dfs(u + 1, path, digits); // 安排下一号位
            path.pop_back(); // 撤销选择
        }
    }
};
```



### 2. 分割回文子串

131\. Palindrome Partitioning  / 分割回文串 (Medium)

[Leetcode](https://leetcode.com/problems/palindrome-partitioning/description/) / [力扣](https://leetcode-cn.com/problems/palindrome-partitioning/description/)

```html
For example, given s = "aab",
Return

[
  ["aa","b"],
  ["a","a","b"]
]
```

**题解**：枚举所有可分割位置和子串长度，需要保存各分割的子串，不满足条件需要回溯。

```java
class Solution {
public:
    vector<vector<string>> res;
    vector<vector<string>> partition(string s) {
        if(s.empty()) return res;
        vector<string> path;
        dfs(0, path, s);
        return res;
    }

    // 枚举各个分割位置
    void dfs(int u, vector<string>& path, string& s) {
        if(u == s.size()) { // 枚举完所有可分割位置
            res.push_back(path);
            return;
        }

        for(int i = 1; i <= s.size(); i++) { // 枚举长度，从1开始
            if(u + i > s.size()) break; // 避免越界
            string split = s.substr(u, i); // 分割子串
            if(valid(split)) { // 子串是回文串
                path.push_back(split); // 选择此子串
                dfs(u + i, path, s); // 下一个分割位置为当前分割位置加当前长度
                path.pop_back(); // 撤销选择
            }
        }
    }

    bool valid(string& s) { // 判断是否为回文
        int begin = 0, end = s.size() - 1;
        while(begin < end) {
            if(s[begin++] != s[end--])
                return false;
        }
        return true;
    }
};
```



### 3. IP 地址划分

93\. Restore IP Addresses / 复原IP地址 (Medium)

[Leetcode](https://leetcode.com/problems/restore-ip-addresses/description/) / [力扣](https://leetcode-cn.com/problems/restore-ip-addresses/description/)

```html
Given "25525511135",
return ["255.255.11.135", "255.255.111.35"].
```

**题解**：通过 DFS 回溯分割 ip 字符串，并进行剪枝。设置变量u扫描 ip 字符串的每个位置，每次移动 i = {1,2,3} 步从而分割出长度为1-3的子串。下一次分割下标从 u + i 开始的子串。当子串个数为4时停止搜索，当 u 移动到字符串末尾即扫描完时保存各子串。长度为3的子串对应的数字不大于255，'0' 开头的子串长度不能超过1，“0”合法，但“01”、“010”不合法。

```cpp
class Solution {
public:
    vector<string> res;
    vector<string> path;
    vector<string> restoreIpAddresses(string s) {
        dfs(0, s);
        return res;
    }
	// u为ip字符串的下标，用于扫描
    void dfs(int u, string &s) {
        if(path.size() == 4) { // 子串数量为4，搜索结束
            if(u == s.size()) { // 下标移动到ip串末尾，保存各子串
                res.push_back(join(path));
            }
            return;
        }

        for(int i = 1; i <= 3; i++) { // 枚举子串长度
            if(u + i > s.size()) break; // 限制子串长度
            string split = s.substr(u, i); // 分割下标为u开始的长度为i的子串

            // 剪枝，只允许首字母为0并且长度为1的子串，0合法但01不合法
            if(split[0] == '0' && split.size() > 1) continue;
            if(i == 3 && stoi(split) > 255) continue; // 长度为3的子串数值最大为255

            path.push_back(split); // 选择此子串
            dfs(u + i, s); // 下次从u + i位置开始枚举
            path.pop_back(); // 撤销选择
        }
    }

    string join(vector<string>& strVec) { // 连接字符串数组并插入'.'
        string tmp;
        for(auto s : strVec) { // 拼接字符串
            tmp += s;
            tmp += ".";
        }
        tmp.pop_back(); // 删除末尾的'.'
        return tmp;
    }
};
```



### 4. 在矩阵中寻找字符串

79\. Word Search / 单词搜索 (Medium)

[Leetcode](https://leetcode.com/problems/word-search/description/) / [力扣](https://leetcode-cn.com/problems/word-search/description/)

```html
For example,
Given board =
[
  ['A','B','C','E'],
  ['S','F','C','S'],
  ['A','D','E','E']
]
word = "ABCCED", -> returns true,
word = "SEE", -> returns true,
word = "ABCB", -> returns false.
```

**题解**：先枚举单词的起点，然后依次枚举单词的每个字母。board中的字母不能重复使用，因而需要设置备忘录。搜到不合法的字符串需要回退，即回溯。

注意：此题通过保存路径的方式处理，较为繁琐难以实现，需处理好起点。**此题返回值为bool类型，说明不需要保存路径，在DFS过程中判断字符合法性即可，也利于剪枝。**

代码1：单独设置备忘录。先枚举可能的起点，然后通过变量 u 枚举单词的每个下标位置，若当前下标对应的字符与board中的字符不匹配则返回false，若所有字符均匹配成功，返回true。

```java
class Solution {
public:
    int m, n;
    vector<vector<int>> memo;
    bool exist(vector<vector<char>>& board, string word) {
        m = board.size(), n = board[0].size();
        if(m == 0 || n == 0) return false;
        memo = vector<vector<int>>(m, vector<int>(n, 0));

        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) { // 枚举各个起点
                if(dfs(i, j, 0, board, word))
                return true;
            }
        }
        return false;
    }
    
    // 通过u枚举单词的每个字母
    bool dfs(int x, int y, int u, vector<vector<char>>& board, string& word) {
        if(word[u] != board[x][y]) return false; // 剪枝，当前字符与当前位置不匹配
        if(u == word.size() - 1) return true; // 最后一个字符正确
        
        memo[x][y] = true; // 标记

        int dx[] = {-1,0,1,0}, dy[] = {0,1,0,-1};
        for(int i = 0; i < 4; i++) { // 做选择
            int tx = x + dx[i], ty = y + dy[i];
            if(tx < 0 || tx >= m || ty < 0 || ty >= n) continue;
            if(memo[tx][ty]) continue;

            if(dfs(tx, ty, u + 1, board, word))
                return true;
        }
        memo[x][y] = false; // 取消标记
        return false;
    }
};
```

代码2：利用board作为备忘录，注意标记的撤销方式。

```cpp
class Solution {
public:
    bool exist(vector<vector<char>>& board, string str) {
        for (int i = 0; i < board.size(); i ++ )
            for (int j = 0; j < board[i].size(); j ++ )
                if (dfs(board, str, 0, i, j))
                    return true;
        return false;
    }

    bool dfs(vector<vector<char>> &board, string &str, int u, int x, int y) {
        if (board[x][y] != str[u]) return false;
        if (u == str.size() - 1) return true;
        int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};
        char t = board[x][y]; // 保存以便恢复
        board[x][y] = '*'; // 标记
        for (int i = 0; i < 4; i ++ ) { // 做选择
            int a = x + dx[i], b = y + dy[i];
            if (a >= 0 && a < board.size() && b >= 0 && b < board[a].size()) {
                if (dfs(board, str, u + 1, a, b)) return true;
            }
        }
        board[x][y] = t; // 撤销标记
        return false;
    }
};
```



### 二、子集排列组合

### 5. 排列

46\. Permutations  / 全排列 (Medium)

[Leetcode](https://leetcode.com/problems/permutations/description/) / [力扣](https://leetcode-cn.com/problems/permutations/description/)

```html
[1,2,3] have the following permutations:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

**[题解](https://leetcode-cn.com/problems/permutations/solution/hui-su-suan-fa-python-dai-ma-java-dai-ma-by-liweiw/)**：每个位置相当于一层，从第一层开始深度优先搜索，枚举可以使用的数字，同时记录已经使用过的数字。

<img src="https://pic.leetcode-cn.com/0bf18f9b86a2542d1f6aa8db6cc45475fce5aa329a07ca02a9357c2ead81eec1-image.png" alt="image.png" style="zoom: 33%;" />

🥇方法1：[**子集、排列、组合通用方法**](https://labuladong.github.io/ebook/%E9%AB%98%E9%A2%91%E9%9D%A2%E8%AF%95%E7%B3%BB%E5%88%97/%E5%AD%90%E9%9B%86%E6%8E%92%E5%88%97%E7%BB%84%E5%90%88.html)。使用vector的pop_back进行撤销，更对称，更容易理解

```cpp
class Solution {
public:
    vector<bool> used;
    vector<int> path;
    vector<vector<int>> res;

    vector<vector<int>> permute(vector<int>& nums) {
        used = vector<bool>(nums.size(), false);
        dfs(0, nums); // 从第0层开始搜索
        return res;
    }

    void dfs(int u, vector<int>& nums) {
        if(u == nums.size()) { // 遍历到最后一层
            res.push_back(path);
            return;
        }
        for(int i = 0; i < nums.size(); i++) { // 枚举当前层可以使用的数，每个可使用的数会形成一条分支
            if(!used[i]) { // 找到一个当前层没有用过的数
                path.push_back(nums[i]); // 做选择
                used[i] = true; // 此数已使用
                dfs(u + 1, nums); // 遍历下一层
                used[i] = false; // 回溯-恢复现场，接下来将遍历另一条路径，此数可重新使用
                path.pop_back(); // 撤销选择
            }
        }
    }
};
```



方法2：非通用方法排列中path的大小固定，每次逐个填入即可。回溯时无需恢复path。

```cpp
class Solution {
public:
    vector<bool> used;
    vector<int> path;
    vector<vector<int>> res;

    vector<vector<int>> permute(vector<int>& nums) {
        used = vector<bool>(nums.size(), false);
        path = vector<int>(nums.size());
        dfs(0, nums); // 从第0层开始搜索
        return res;
    }

    void dfs(int cur_pos, vector<int>& nums) {
        if(cur_pos == nums.size()) { // 遍历到最后一层
            res.push_back(path);
            return;
        }
        for(int i = 0; i < nums.size(); i++) { // 枚举当前层可以使用的数，每个可使用的数会形成一条分支
            if(!used[i]) { // 选择一个当前层没有用过的数
                path[cur_pos] = nums[i];

                used[i] = true; // 标记
                dfs(cur_pos + 1, nums); // 遍历下一层
                used[i] = false; // 回溯-恢复现场，接下来将遍历另一条路径，此数可重新使用
            }
        }
    }
};
```



### 6. 含有重复元素求排列

47\. Permutations II / 全排列 II (Medium)

【[Leetcode](https://leetcode.com/problems/permutations-ii/description/) / [力扣](https://leetcode-cn.com/problems/permutations-ii/description/) 】 给定一个可包含重复数字的序列，返回所有不重复的全排列。

```html
[1,1,2] have the following unique permutations:
[[1,1,2], [1,2,1], [2,1,1]]
```

**[题解](https://leetcode-cn.com/problems/permutations-ii/solution/hui-su-suan-fa-python-dai-ma-java-dai-ma-by-liwe-2/)**：数组元素可能含有相同的元素，进行排列时就有可能出现重复的排列，要求重复的排列只返回一个。

![image-20200526191652642](https://gitee.com//MrRen-sdhm/Images/raw/master/img/20200526191656.png)



🥇方法1:

在实现上，和 Permutations 不同的是**要先排序**，然后在添加一个元素时，判断这个元素是否等于前一个元素，如果等于，并且**前一个元素还未访问**（说明当前元素是回溯操作恢复的，即是重复的；若前一个元素已访问，说明当前元素不是回溯操作恢复的，即不是重复的），那么就跳过这个元素。

```java
class Solution {
public:
    vector<bool> used;
    vector<int> path;
    vector<vector<int>> res;

    vector<vector<int>> permuteUnique(vector<int>& nums) {
        used = vector<bool>(nums.size(), false);
        path = vector<int>(nums.size());
        sort(nums.begin(), nums.end()); // 排序
        dfs(0, nums); // 从第0层开始搜索
        return res;
    }

    void dfs(int cur_pos, vector<int>& nums) {
        if(cur_pos == nums.size()) { // 遍历到最后一层
            res.push_back(path);
            return;
        }
        for(int i = 0; i < nums.size(); i++) { // 枚举当前层可以使用的数，每个可使用的数会形成一条分支
            if(used[i]) continue; // 找到一个当前层没有用过的数
            // 剪枝避免重复，若当前数等于前一个数并且前一个数的选择被撤销，跳过
            if(i > 0 && !used[i - 1] && nums[i] == nums[i - 1]) continue;
            path[cur_pos] = nums[i];
            
            used[i] = true; // 此数已使用
            dfs(cur_pos + 1, nums);
            used[i] = false; // 回溯-恢复现场，接下来将遍历另一条路径，此数可重新使用
        }
    }
};
```



[方法2](https://www.acwing.com/solution/LeetCode/content/126/)：

1. 先将所有数从小到大排序，这样相同的数会排在一起；
2. 从左到右依次枚举每个数，每次将它放在一个空位上；
3. 对于相同数，我们人为定序，就可以避免重复计算：我们在dfs时记录一个额外的状态，记录上一个相同数存放的位置 start，我们在枚举当前数时，只枚举 start+1,start+2,…,nstart+1,start+2,…,n 这些位置。
4. 不要忘记递归前和回溯时，对状态进行更新。

```cpp
class Solution {
public:
    vector<bool> used;
    vector<int> path;
    vector<vector<int>> res;

    vector<vector<int>> permutation(vector<int>& nums) {
        used = vector<bool>(nums.size(), false);
        path = vector<int>(nums.size());
        sort(nums.begin(), nums.end()); // 排序
        dfs(0, 0, nums); // 从第0层开始搜索
        return res;
    }

    void dfs(int cur_pos, int start, vector<int>& nums) {
        if(cur_pos == nums.size()) { // 遍历到最后一层
            res.push_back(path);
            return;
        }
        for(int i = start; i < nums.size(); i++) { // 枚举当前层可以使用的数，每个可使用的数会形成一条分支
            if(used[i]) continue; // 找到一个当前层没有用过的数
            path[i] = nums[cur_pos];
            
            used[i] = true; // 此数已使用
            if(nums[cur_pos + 1] != nums[cur_pos] && cur_pos + 1 < nums.size())
                dfs(cur_pos + 1, 0, nums); // 遍历下一层
            else
                dfs(cur_pos + 1, i + 1, nums);
            used[i] = false; // 回溯-恢复现场，接下来将遍历另一条路径，此数可重新使用
        }
    }
};
```



### 7. 组合

77\. Combinations / 组合 (Medium)

[Leetcode](https://leetcode.com/problems/combinations/description/) / [力扣](https://leetcode-cn.com/problems/combinations/description/)  给定两个整数 *n* 和 *k*，返回 1 ... *n* 中所有可能的 *k* 个数的组合。

```html
If n = 4 and k = 2, a solution is:
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

**题解**：可对比[子集问题](#_11-子集)，相当于保存了子集问题中深度为k的层。下图为子集问题的回溯树，第3行的元素即为k=2时的组合。即在子集问题的基础上，把保存结果的操作放到树的深度为k时即可。详见后面的代码对比。

<img src="https://pic.leetcode-cn.com/83b914f06fefb895af2f21629087aab8168c1277c8c7b8cdfc4f7e475ee2e651.jpg" alt="img" style="zoom: 50%;" />

[**子集、排列、组合通用方法**](https://labuladong.github.io/ebook/%E9%AB%98%E9%A2%91%E9%9D%A2%E8%AF%95%E7%B3%BB%E5%88%97/%E5%AD%90%E9%9B%86%E6%8E%92%E5%88%97%E7%BB%84%E5%90%88.html)。需要设置一个start变量，**排除已选择的数字**。

```cpp
class Solution {
public:
    vector<vector<int>>res;

    vector<vector<int>> combine(int n, int k) {
        if (k <= 0 || n <= 0) return res;
        vector<int> path;
        dfs(n, k, 1, path);
        return res;
    }

    void dfs(int n, int k, int start, vector<int>& path) {
        if (k == path.size()) { // 到达树的底部
            res.push_back(path);
            return;
        }
        for (int i = start; i <= n; i++) { // 注意i从start开始递增
            path.push_back(i); // 做选择
            dfs(n, k, i + 1, path);
            path.pop_back(); // 撤销选择
        }
    }
};
```

**子集、组合问题代码对比**：这里将上述组合问题的输入改为一个数组，以便于对比。可以看到，两者的区别只有一点：**结果的保存相关代码不同**。

- 子集问题保存所有路径上的数据
- 组合问题仅保存指定层上的数据
- 相当于组合问题的结果是子集问题的结果的子集

```cpp
// 子集
class Solution {
public:
    vector<vector<int>> res;
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<int> path; // 记录走过的路径
        dfs(0, path, nums);
        return res;
    }

    void dfs(int start, vector<int>& path, vector<int>& nums) {
        res.push_back(path); /*** 保存所有路径点的数据 */
        for(int i = start; i < nums.size(); i++) {
            path.push_back(nums[i]); // 做选择
            dfs(i + 1, path, nums);
            path.pop_back(); // 撤销选择
        }
    }
};

// 组合
class Solution {
public:
    vector<vector<int>>res;
    vector<vector<int>> combine(vector<int>& nums, int k) {
        if (k <= 0 || nums.size() <= 0 || k > nums.size()) return res;
        vector<int> path;
        dfs(0, k, path, nums);
        return res;
    }

    void dfs(int start, int k, vector<int>& path, vector<int>& nums) {
        if (k == path.size()) { /*** 仅保存指定层上路径点的数据 */
            res.push_back(path);
            return; // 到达指定深度可直接返回
        }
        for (int i = start; i < nums.size(); i++) { // 注意i从start开始递增
            path.push_back(nums[i]); // 做选择
            dfs(i + 1, k, path, nums);
            path.pop_back(); // 撤销选择
        }
    }
};
```



### 8. 组合求和

39\. Combination Sum / 组合总和 (Medium)

[Leetcode](https://leetcode.com/problems/combination-sum/description/) / [力扣 ](https://leetcode-cn.com/problems/combination-sum/description/)  给定一个无重复元素的数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。candidates 中的数字**可以无限制重复被选取**。

```html
given candidate set [2, 3, 6, 7] and target 7,
A solution set is:
[[7],[2, 2, 3]]
```

**题解**：此题中数字**可以无限制重复被选取**，因而**下一次搜索的起始位置是当前位置**，而不是下一位置。即`dfs(i, path, candidates, target);`传入的是 i 而不是 i + 1。注意与上题中求组合的区别。

另外需要注意的是，需保证每个分支的target相同，因而需要恢复现场。

```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        if(candidates.empty()) return res;
        vector<int> path;
        dfs(0, path, candidates, target);
        return res;
    }

    void dfs(int start, vector<int>& path, vector<int>& candidates, int target) {
        if(target == 0) { // 找到目标组合
            res.push_back(path);
            return;
        }

        for(int i = start; i < candidates.size(); i++) {
            if(target < candidates[i]) continue; // 非法性剪枝

            path.push_back(candidates[i]); // 选择
            target -= candidates[i]; // 目标值减小
            dfs(i, path, candidates, target); // 可以重复使用元素，因而下次从当前元素开始搜
            target += candidates[i]; // 恢复目标值
            path.pop_back(); // 撤销选择
        }
    }
};
```



### 9. 含有相同元素的组合求和

40\. Combination Sum II / 组合总和 II (Medium)

[Leetcode](https://leetcode.com/problems/combination-sum-ii/description/) / [力扣](https://leetcode-cn.com/problems/combination-sum-ii/description/)  给定一个数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。candidates 中的**每个数字在每个组合中只能使用一次**。

说明：所有数字（包括目标数）都是正整数。**解集不能包含重复的组合**。 

```html
For example, given candidate set [10, 1, 2, 7, 6, 1, 5] and target 8,
A solution set is:
[
  [1, 7],
  [1, 2, 5],
  [2, 6],
  [1, 1, 6]
]
```

**题解**：与上题不同的是，给定的数字数组中**有重复元素**，并且**每个元素不可重复使用**。需要做两处修改：

- 为避免使用数组中的重复数字，需要先排序，并设置备忘录，通过剪枝避免使用数组中相同的元素。
- 同一元素不可使用两次，因而在搜索时从下一元素开始搜

```cpp
class Solution {
public:
    vector<vector<int>> res;
     vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        if(candidates.empty()) return res;
        sort(candidates.begin(), candidates.end()); // 排序
        vector<int> path;
        vector<bool> memo(candidates.size());
        dfs(0, path, memo, candidates, target);
        return res;
    }

    void dfs(int start, vector<int>& path, vector<bool>& memo, vector<int>& candidates, int target) {
        if(target == 0) { // 找到目标组合
            res.push_back(path);
            return;
        }

        for(int i = start; i < candidates.size(); i++) {
            if(target < candidates[i]) continue; // 非法性剪枝
            if(i > 0 && candidates[i] == candidates[i - 1] && !memo[i - 1]) continue; // 剪枝，避免重复使用同一数字

            path.push_back(candidates[i]); // 选择
            memo[i] = true; // 标记
            target -= candidates[i]; // 目标值减小
            dfs(i + 1, path, memo, candidates, target); // 不可以重复使用元素，因而下次从下一元素开始搜
            target += candidates[i]; // 恢复目标值
            memo[i] = false; // 取消标记
            path.pop_back(); // 撤销选择
        }
    }
};
```



### 10. 1-9 数字的组合求和

216\. Combination Sum III / 组合总和 III (Medium)

[Leetcode](https://leetcode.com/problems/combination-sum-iii/description/) / [力扣](https://leetcode-cn.com/problems/combination-sum-iii/description/)

```html
Input: k = 3, n = 9

Output:

[[1,2,6], [1,3,5], [2,3,4]]
```

从 1-9 数字中选出 k 个数不重复的数，使得它们的和为 n。

**题解**：这题和组合求和没有多大区别，在path中元素个数为k时保存即可。

```cpp
class Solution {
public:
    vector<vector<int>> res;
    vector<vector<int>> combinationSum3(int k, int n) {
        vector<int> nums = {1,2,3,4,5,6,7,8,9};
        vector<int> path;
        dfs(0, path, nums, k, n);
        return res;
    }

    void dfs(int start, vector<int>& path, vector<int>& nums, int k, int target) {
        if(k == path.size() && target == 0) { // 保存个数为k并且和为target的组合
            res.push_back(path);
            return;
        }
        
        for(int i = start; i < nums.size(); i++) {
            if(target < nums[i]) continue; // 非法性剪枝

            path.push_back(nums[i]); // 选择
            target -= nums[i];
            dfs(i + 1, path, nums, k, target);
            target += nums[i];
            path.pop_back(); // 撤销选择
        }
    }
};
```



### 11. 子集

78\. Subsets  / 子集 (Medium)

[Leetcode](https://leetcode.com/problems/subsets/description/) / [力扣](https://leetcode-cn.com/problems/subsets/description/)

找出集合的所有子集，子集不能重复，[1, 2] 和 [2, 1] 这种子集算重复

```html
输入: nums = [1,2,3]
输出:
[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

**题解**：

[方法1](https://labuladong.gitbook.io/algo/suan-fa-si-wei-xi-lie/zi-ji-pai-lie-zu-he#yi-zi-ji)：[**子集、排列、组合通用方法**](https://labuladong.github.io/ebook/%E9%AB%98%E9%A2%91%E9%9D%A2%E8%AF%95%E7%B3%BB%E5%88%97/%E5%AD%90%E9%9B%86%E6%8E%92%E5%88%97%E7%BB%84%E5%90%88.html)。深度优先遍历，回溯。设置一个start变量，**排除已选择的数字**。

需要注意的是：

- **子集问题中for循环中的每次dfs搜索的位置是不同的，其搜索起点根据for中的下标 i 改变，因而每个分支的搜索空间不一样，越往右可选择的数越少，因而搜索空间越小。**
- 可以对比排列解法中for循环中的dfs，每个循环中dfs搜索的起点是根据当前的层数 u 而不是for循环的下标 i 来确定的，因而其搜索起点是一样的。即每个分支的搜索空间是一样的。

<img src="https://pic.leetcode-cn.com/83b914f06fefb895af2f21629087aab8168c1277c8c7b8cdfc4f7e475ee2e651.jpg" alt="img" style="zoom: 50%;" />

上图中对应的start变量的变化如下：

```
                                            0
                                          /\  \
                                         1  2  3
                                        / \  \
                                       2   3  3
                                      /
                                     3
```



```java
class Solution {
public:
    vector<vector<int>> res;
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<int> path; // 记录走过的路径
        dfs(0, path, nums);
        return res;
    }

    void dfs(int start, vector<int>& path, vector<int>& nums) {
        res.push_back(path);
        for(int i = start; i < nums.size(); i++) {
            path.push_back(nums[i]); // 做选择
            dfs(i + 1, path, nums);
            path.pop_back(); // 撤销选择
        }
    }
};
```

[方法2](https://leetcode-cn.com/problems/subsets/solution/liang-chong-fang-fa-qiu-jie-zi-ji-by-tangzixia/)：层序遍历

<img src="https://pic.leetcode-cn.com/03408dfe78564b721a065bf3bb34bc4e933f321a6e5e5883f0a5096a88dadb0b-%E8%A7%A3%E6%9E%90.png" alt="解析.png"  />

```cpp
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> res(1); // 初始化一个空元素

        for(int i = 0; i < nums.size(); i++) {
            int cnt = res.size();
            for(int j = 0; j < cnt; j++) {
                vector<int> tmp = res[j];
                tmp.push_back(nums[i]);
                res.push_back(tmp);
            }
        }
        return res;
    }
};
```



### 12. 含有相同元素求子集

90\. Subsets II / 子集 II (Medium)

[Leetcode](https://leetcode.com/problems/subsets-ii/description/) / [力扣](https://leetcode-cn.com/problems/subsets-ii/description/)

```html
For example,
If nums = [1,2,2], a solution is:

[
  [2],
  [1],
  [1,2,2],
  [2,2],
  [1,2],
  []
]
```

**题解**：与含有相同元素求排列类似，需要进行剪枝。**start变量仅能够在元素不重复的情况下保证不会选择之前选过的元素**。因而需要额外设置一个备忘录，标记已访问的元素，以避免重复选择相同的两个数。剪枝的条件即为，当前元素与前一个元素相等，并且前一个元素刚刚被设置为未访问，说明前一个元素由回溯得到，此次不可再重复使用。

```java
class Solution {
public:
    vector<vector<int>> res;
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        if(nums.empty()) return res;
        sort(nums.begin(), nums.end()); // 排序
        vector<int> path;
        vector<bool> memo(nums.size()); // 设置备忘录
        
        dfs(0, path, memo, nums);
        return res;
    }

    void dfs(int start, vector<int>& path, vector<bool>& memo, vector<int>& nums) {
        res.push_back(path);
        for(int i = start; i < nums.size(); i++) {
            if(i > 0 && nums[i] == nums[i - 1] && !memo[i - 1]) continue; // 剪枝，前一个元素未访问，即回溯得到

            path.push_back(nums[i]); // 选择
            memo[i] = true; // 标记
            dfs(i + 1, path, memo, nums);
            memo[i] = false; // 撤销标记
            path.pop_back(); // 撤销选择
        }
    }
};
```



### 三、其他类型

### 1. 括号生成

【[Leetcode 22. 括号生成](https://leetcode-cn.com/problems/generate-parentheses/)】  数字 *n* 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 **有效的** 括号组合。

```
输入：n = 3
输出：[
       "((()))",
       "(()())",
       "(())()",
       "()(())",
       "()()()"
     ]
```

**题解**：合法条件：

- 一个「合法」括号组合的左括号数量一定等于右括号数量。
- 对于一个「合法」的括号字符串组合，其子串中左括号的数量都大于或等于右括号的数量。

即如果左括号数量小于n，可以放一个左括号。如果右括号数量小于左括号的数量，可以放一个右括号。

<img src="https://pic.leetcode-cn.com/efbe574e5e6addcd1c9dc5c13a50c6f162a2b14a95d6aed2c394e18287a067fa-image.png" alt="image.png" style="zoom: 33%;" />

```cpp
class Solution {
public:
    vector<string> res;
    vector<string> generateParenthesis(int n) {
        string path;
        dfs(0, 0, path, n);
        return res;
    }

    void dfs(int left, int right, string& path, int n) {
        if(left == n && right == n) {
            res.push_back(path);
            return;
        }

        if(left < n) { // 左括号数量小于n，可以放一个左括号
            path.push_back('(');
            dfs(left + 1, right, path, n);
            path.pop_back();
        }

        if(right < left) { // 右括号数量小于左括号的数量，可以放一个右括号
            path.push_back(')');
            dfs(left, right + 1, path, n);
            path.pop_back();
        }
    }
};
```



### 2. 数独

37\. Sudoku Solver / 数独 (Hard)

[Leetcode](https://leetcode.com/problems/sudoku-solver/description/) / [力扣](https://leetcode-cn.com/problems/sudoku-solver/description/)

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/0e8fdc96-83c1-4798-9abe-45fc91d70b9d.png"/> </div><br>

```java
private boolean[][] rowsUsed = new boolean[9][10];
private boolean[][] colsUsed = new boolean[9][10];
private boolean[][] cubesUsed = new boolean[9][10];
private char[][] board;

public void solveSudoku(char[][] board) {
    this.board = board;
    for (int i = 0; i < 9; i++)
        for (int j = 0; j < 9; j++) {
            if (board[i][j] == '.') {
                continue;
            }
            int num = board[i][j] - '0';
            rowsUsed[i][num] = true;
            colsUsed[j][num] = true;
            cubesUsed[cubeNum(i, j)][num] = true;
        }
        backtracking(0, 0);
}

private boolean backtracking(int row, int col) {
    while (row < 9 && board[row][col] != '.') {
        row = col == 8 ? row + 1 : row;
        col = col == 8 ? 0 : col + 1;
    }
    if (row == 9) {
        return true;
    }
    for (int num = 1; num <= 9; num++) {
        if (rowsUsed[row][num] || colsUsed[col][num] || cubesUsed[cubeNum(row, col)][num]) {
            continue;
        }
        rowsUsed[row][num] = colsUsed[col][num] = cubesUsed[cubeNum(row, col)][num] = true;
        board[row][col] = (char) (num + '0');
        if (backtracking(row, col)) {
            return true;
        }
        board[row][col] = '.';
        rowsUsed[row][num] = colsUsed[col][num] = cubesUsed[cubeNum(row, col)][num] = false;
    }
    return false;
}

private int cubeNum(int i, int j) {
    int r = i / 3;
    int c = j / 3;
    return r * 3 + c;
}
```



### 3. N 皇后

51\. N-Queens / N皇后 (Hard)

[Leetcode](https://leetcode.com/problems/n-queens/description/) / [力扣](https://leetcode-cn.com/problems/n-queens/description/) / [Acwing](https://www.acwing.com/problem/content/description/845/)

在 n\*n 的矩阵中摆放 n 个皇后，并且每个皇后不能在同一行，同一列，同一对角线上，求所有的 n 皇后的解。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/067b310c-6877-40fe-9dcf-10654e737485.jpg"/> </div><br>

**题解**：[题解1](https://leetcode-cn.com/problems/n-queens/solution/gen-ju-di-46-ti-quan-pai-lie-de-hui-su-suan-fa-si-/)  [题解2](https://www.acwing.com/solution/acwing/content/2820/)

一行一行地摆放，在确定一行中的那个皇后应该摆在哪一列时，需要用三个标记数组来确定某一列是否合法，这三个标记数组分别为：列标记数组、45 度对角线标记数组和 135 度对角线标记数组。

45 度对角线标记数组的长度为 2 \* n - 1，通过下图可以明确 (r, c) 的位置所在的数组下标为 r + c。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/9c422923-1447-4a3b-a4e1-97e663738187.jpg" width="300px"> </div><br>

135 度对角线标记数组的长度也是 2 \* n - 1，(r, c) 的位置所在的数组下标为 (r - c) + n - 1。

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/7a85e285-e152-4116-b6dc-3fab27ba9437.jpg" width="300px"> </div><br>

```cpp
class Solution {
public:
    vector<vector<string>> res;
    vector<string> nQuee;
    vector<bool> col, master, slave; // 记录选过的列，对角线，反对角线
    vector<vector<string>> solveNQueens(int n) {
        nQuee = vector<string>(n, string(n, '.')); // 初始化为n行n列个字符
        col = vector<bool>(n, false);
        master = slave = vector<bool>(2*n-1, false); // 对角线最多元素个数为2*n-1
        dfs(0, n);
        return res;
    }

    void dfs(int row, int n) {
        if(row == n) { // 遍历到最后一层
            res.push_back(nQuee);
            return;
        }
        for(int j = 0; j < n; j++) { // 枚举每行可以选择的位置
            // 找到一个所在列未选择，正反对角线未选择的位置
            if(!col[j] && !master[row + j] && !slave[(row - j) + n - 1]) {
                nQuee[row][j] = 'Q';
                col[j] = master[row + j] = slave[(row - j) + n - 1] = true;
                dfs(row + 1, n); // 遍历下一行
                // 恢复现场
                col[j] = master[row + j] = slave[(row - j) + n - 1] = false;
                nQuee[row][j] = '.';
            }
        }
    }
};
```

非class解法1：利用全排列的思想按行枚举，即已分析出每列只能有一个皇后，复杂度O(n!)

```cpp
#include <iostream>
using namespace std;

const int N = 10;
int n;
bool col[N], dg[N], udg[N]; // 记录选过的列，对角线，反对角线
char g[N][N];

void dfs(int u) {
    if(u == n) { // 遍历完所有行
        for(int i = 0; i < n; i++)
            cout << g[i] << endl;
        cout << endl;
        return;
    }
    for(int i = 0; i < n; i++) { // 枚举每行可以选择的位置
        if(!col[i] && !dg[i + u] && !udg[n - u + i]) { // 找到一个所在列未选择，正反对角线未选择的位置
            g[u][i] = 'Q';
            col[i] = dg[i + u] = udg[n - u + i] = true;
            dfs(u + 1); // 遍历下一行
            // 恢复现场
            col[i] = dg[i + u] = udg[n - u + i] = false;
            g[u][i] = '.';
        }
    }
}

int main () {
    cin >> n;
    for(int i = 0; i < n; i++) {
        for(int j = 0; j < n; j++)
            g[i][j] = '.';
    }
    dfs(0); // 从第0行开始搜索
    return 0;
}
```

非class解法2：枚举每个位置选还是不选，总共有n^2^个位置，每个位置都有两种情况，时间复杂度O(2的n^2^次方)

不同搜索顺序时间复杂度不同  所以搜索顺序很重要！

```cpp
#include <iostream>
using namespace std;

const int N = 20;
int n;
bool row[N], col[N], dg[N], udg[N]; // 记录选过的行，列，对角线，反对角线
char g[N][N];

// 行、列、当前已放置皇后数
void dfs(int x, int y, int s) {
    if(y == n) y = 0, x++; // 处理超过边界的情况
    
    if(x == n) { // 遍历完所有行，枚举完n^2个位置
        if(s == n) { // 已放置n个皇后，打印
            for(int i = 0; i < n; i++)
                cout << g[i] << endl;
            cout << endl;
        }
        return; // 枚举完所有位置，返回
    }
    
    // 不放皇后，搜下一个位置
    dfs(x, y + 1, s);
    
    // 放皇后
    if(!row[x] && !col[y] && !dg[x + y] && !udg[x - y + n]) { // 所在行列及正反对角线无皇后
        g[x][y] = 'Q';
        row[x] = col[y] = dg[x + y] = udg[x - y + n] = true;
        dfs(x, y + 1, s + 1); // 搜下一个位置，已放皇后数加1
        // 恢复现场
        row[x] = col[y] = dg[x + y] = udg[x - y + n] = false;
        g[x][y] = '.';
    }
}

int main () {
    cin >> n;
    for(int i = 0; i < n; i++) {
        for(int j = 0; j < n; j++)
            g[i][j] = '.';
    }
    dfs(0, 0, 0); // 从第0行0列开始搜索，初始已放置0个
    return 0;
}
```



### 4. 火柴拼正方形

[Leetcode 473. 火柴拼正方形](https://leetcode-cn.com/problems/matchsticks-to-square/)

**题解：**此题即判断一个数组能否分成四个相等的部分，可先创建包含四个元素的数组 sum[4] 存储各个边的和，可通过DFS枚举数组的各个位置，将当前位置的数字加到各个边的和上，枚举到最后一个元素时判断各部分和是否相等即可。为优化搜索，需要进行剪枝或优化：

- 剪枝：当元素和超过目标值时直接跳过，不加入当前元素

- 搜索顺序优化：先对数组从大到小排序，使大的元素先相加，这样便可以快速判断当前路径是否合法，从而避免进行冗余的搜索。

```cpp
class Solution {
public:
    int sum = 0;
    vector<int> edgSum;
    bool makesquare(vector<int>& nums) {
        if(nums.empty()) return false;
        for(int num : nums)
            sum += num;
        if(sum % 4 != 0) return false;
        sort(nums.rbegin(), nums.rend()); // 从大到小排序

        edgSum = vector<int>(4, 0); // 各个边的和
        return dfs(0, nums);
    }

    bool dfs(int u, vector<int>& nums) {
        if(u == nums.size()) {
            if(edgSum[0] == edgSum[1] && edgSum[1] == edgSum[2] && edgSum[2] == edgSum[3])
                return true;
            return false;
        }
        
        for(int i = 0; i < 4; i++) {
            if(edgSum[i] + nums[u] > sum / 4) continue; // 剪枝，加入此元素后当前边的和超过目标值

            edgSum[i] += nums[u];
            if(dfs(u + 1, nums)) return true; // 找到答案直接返回即可
            edgSum[i] -= nums[u];
        }
        return false;
    } 
};
```

