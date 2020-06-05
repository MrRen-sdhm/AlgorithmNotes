# 树✏️🥇⭐️❌

# 递归

一棵树要么是空树，要么有两个指针，每个指针指向一棵树。树是一种递归结构，很多树的问题可以使用递归来处理。

- **双根递归**：若需要判断两棵树之间的关系，需要将两棵树的根节点传入递归函数，同时进行遍历。可解决的问题：
  - **与两棵树有关的问题**，如判断一棵树是不是另一棵树的[子树](##8. 子树⭐️)
  - **判断一棵树的子树的某些性质**，如[路径和III](##7. 统计路径和等于一个数的路径数量⭐️)
  - **一棵树的某些部分之间的关系**，如[树的对称](##9. 树的对称)

- **双递归**：需要判断一棵树的**所有子树**与另一棵树的关系，那么就需要对这颗树本身进行递归遍历，以获取各个子树根节点，然后通过双根递归判断各子树与另一棵树的关系，因而会用到两个递归函数，可以用于解决与子树相关的问题。
  - 思想即主函数递归遍历树的每个节点，调用辅助函数对每个节点开始的子树的性质进行判断，并且辅助函数也为递归函数，用于对子树进行遍历。



## 1. 最大深度⭐️

104\. Maximum Depth of Binary Tree / 二叉树的最大深度 (Easy)

[Leetcode](https://leetcode.com/problems/maximum-depth-of-binary-tree/description/) / [力扣](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/description/)

**题解：**

方法1：递归

<img src="https://pic.leetcode-cn.com/Figures/104/104_slide_10.png" alt="img" style="zoom: 33%;" />

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

方法2：DFS

```cpp
class Solution {
public:
    int res = 0;
    int maxDepth(TreeNode* root) {
        dfs(1, root);
        return res;
    }

    void dfs(int cnt, TreeNode* root) {
        if(!root) return;
        if(!root->left && !root->right) res = max(res, cnt); // 叶子节点

        dfs(cnt + 1, root->left);
        dfs(cnt + 1, root->right);
        return;
    }
};
```

方法3：BFS

```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(!root) return 0;
        return bfs(root);
    }

    int bfs(TreeNode* root) {
        queue<TreeNode*> qu;
        qu.push(root);
        int cnt = 0;

        while(!qu.empty()) {
            int size = qu.size();
            cnt++;
            while(size--) {
                auto t = qu.front(); qu.pop();

                if(t->left) qu.push(t->left);
                if(t->right) qu.push(t->right);
            }
        }
        return cnt;
    }
};
```



## 2. 最小深度

111\. Minimum Depth of Binary Tree / 二叉树的最小深度 (Easy)

[Leetcode](https://leetcode.com/problems/minimum-depth-of-binary-tree/description/) / [力扣](https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/description/)

树的根节点到叶子节点的最小路径长度

**题解：**

方法1：递归

**需要对左子节点或右子节点为空的情况进行处理**。例如：若左子节点为空，则以当前节点为根节点的子树的最小深度为右子树最小深度加1，而不是左右子树深度最小值的最小值加1

```cpp
class Solution {
public:
    int minDepth(TreeNode* root) {
        if(!root) return 0;
        int left = minDepth(root->left);
        int right = minDepth(root->right);
        if(left == 0 || right == 0) return left + right + 1; // 处理左子节点或右子节点为空
        return min(left, right) + 1;
    }
};
```

**注意**：求最小深度与求最大深度的代码类似，但还需对左子节点或者右子节点为空的情况进行处理。例如以下二叉树，节点 2 的深度为 1，节点NULL的深度为 0， 如果直接用 `min(left, right) + 1` 求得最终的结果为 `min(0, 1) + 1 = 1`，因而最终结果为1，而正确答案为2。原因就在于NULL节点的深度设为0了，对求最小值有影响，对求最大值无影响。

```
  1
 / \
2  NULL
```



方法2：DFS，与求最大深度没什么区别，但返回值需初始化为正无穷

```cpp
class Solution {
public:
    int res = 0x7FFFFFFF;
    int minDepth(TreeNode* root) {
        if(!root) return 0;
        dfs(root, 1);
        return res;
    }

    void dfs(TreeNode* root, int cnt) {
        if(!root) return;
        if(!root->left && !root->right) res = min(res, cnt); // 叶子节点

        dfs(root->left, cnt + 1);
        dfs(root->right, cnt + 1);
        return;
    }
};
```



## 3. 平衡树

110\. Balanced Binary Tree / 平衡二叉树 (Easy)

[Leetcode](https://leetcode.com/problems/balanced-binary-tree/description/) / [力扣](https://leetcode-cn.com/problems/balanced-binary-tree/description/)

```html
    3
   / \
  9  20
    /  \
   15   7
```

平衡树左右子树高度差都小于等于 1

**题解：**根据上题求树的高度的递归写法，进行修改即可得到DFS版本的解法

```cpp
class Solution {
public:
    bool res = true;
    bool isBalanced(TreeNode* root) {
        dfs(root);
        return res;
    }

    int dfs(TreeNode* root) { // 计算二叉树的深度
        if(!root) return 0;
        int left = dfs(root->left);
        int right = dfs(root->right);
        if(abs(left - right) > 1) res = false; // 深度差超过1
        return max(left, right) + 1;
    }
};
```



## 4. 两节点的最长路径

543\. Diameter of Binary Tree / 二叉树的直径 (Easy)

[Leetcode](https://leetcode.com/problems/diameter-of-binary-tree/description/) / [力扣](https://leetcode-cn.com/problems/diameter-of-binary-tree/description/)

```html
Input:

         1
        / \
       2  3
      / \
     4   5

Return 3, which is the length of the path [4,2,1,3] or [5,2,1,3].
```

**题解：**需要注意的是，最长路径不一定经过根节点。计算每个节点的左右子树的深度的和，求各节点左右子树深度和的最大值即可，同样是使用深度优先遍历，定义一个全局变量保存最长路径的长度即可。

<img src="https://pic.leetcode-cn.com/baf2f6ea1ae76ba383eb1753254340f089dac9f03664f93990d6ae54f8560970-image.png" alt="image.png" style="zoom: 25%;" />

```cpp
class Solution {
public:
    int maxDepthSum;
    int diameterOfBinaryTree(TreeNode* root) {
        maxDepth(root);
        return maxDepthSum;
    }

    int maxDepth(TreeNode* root) {
        if(!root) return 0;
        int left = maxDepth(root->left);
        int right = maxDepth(root->right);
        maxDepthSum = max(maxDepthSum, left + right);
        return max(left, right) + 1;
    }
};
```

## 5. 翻转树

226\. Invert Binary Tree / 翻转二叉树 (Easy)

[Leetcode](https://leetcode.com/problems/invert-binary-tree/description/) / [力扣](https://leetcode-cn.com/problems/invert-binary-tree/description/)

**题解：**

方法1：递归

- 终止条件：当前节点为null时返回
- 交换当前节点的左右节点，再递归的交换当前节点的左节点的左右子树，再递归的交换当前节点的右节点的左右子树，相当于递归先序遍历

<img src="https://pic.leetcode-cn.com/0f91f7cbf5740de86e881eb7427c6c3993f4eca3624ca275d71e21c5e3e2c550-226_2.gif" alt="226_2.gif" style="zoom: 50%;" />

```cpp
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if(!root) return NULL;
        swap(root->left, root->right); // 交换当前节点的左右子树
        invertTree(root->left); // 递归交换左子节点的左右子树
        invertTree(root->right); // 递归交换右子节点的左右子树
        return root;
    }
};
```

方法2：迭代，类似于BFS。将二叉树中的节点逐层放入队列中，再迭代处理队列中的元素，处理操作即交换当前队头节点的左右子树

<img src="https://pic.leetcode-cn.com/f9e06159617cbf8372b544daee37be70286c3d9b762c016664e225044fc4d479-226_%E8%BF%AD%E4%BB%A3.gif" alt="226_迭代.gif" style="zoom:50%;" />

```cpp
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if(!root) return NULL;
        queue<TreeNode*> qu;
        qu.push(root);

        while(!qu.empty()) {
            auto t = qu.front(); qu.pop();
            swap(t->left, t->right); // 交换当前节点的左右子树
            if(t->left) qu.push(t->left);
            if(t->right) qu.push(t->right);
        }
        return root;
    }
};
```



## 6. 归并两棵树

617\. Merge Two Binary Trees  / 合并二叉树 (Easy)

[Leetcode](https://leetcode.com/problems/merge-two-binary-trees/description/) / [力扣](https://leetcode-cn.com/problems/merge-two-binary-trees/description/)

```html
Input:
       Tree 1                     Tree 2
          1                         2
         / \                       / \
        3   2                     1   3
       /                           \   \
      5                             4   7

Output:
         3
        / \
       4   5
      / \   \
     5   4   7
```

**题解：**

方法1：递归

对这两棵树同时进行**前序遍历**，并将对应的节点进行合并。在遍历时，如果两棵树的当前节点均不为空，就将它们的值进行相加，并对它们的左孩子和右孩子进行递归合并；如果其中有一棵树为空，那么我们返回另一颗树作为结果；如果两棵树均为空，此时返回任意一棵树均可（因为都是空）

这里将结果存储到树1中，当然也可以新建一棵树

```cpp
class Solution {
public:
    TreeNode* mergeTrees(TreeNode* t1, TreeNode* t2) {
        if(!t1 && !t2) return NULL;
        if(!t1) return t2;
        if(!t2) return t1;

        t1->val += t2->val; // 相同位置的值相加
        t1->left = mergeTrees(t1->left, t2->left); // 合并两棵树的左子树
        t1->right = mergeTrees(t1->right, t2->right); // 合并两棵树的右子树
        return t1;
    }
};
```

方法2：迭代，使用栈，见[官方题解](https://leetcode-cn.com/problems/merge-two-binary-trees/solution/he-bing-er-cha-shu-by-leetcode/)



## 7. 判断路径和是否等于一个数

Leetcdoe : 112. Path Sum / 路径总和 (Easy)

[Leetcode](https://leetcode.com/problems/path-sum/description/) / [力扣](https://leetcode-cn.com/problems/path-sum/description/)

```html
Given the below binary tree and sum = 22,

              5
             / \
            4   8
           /   / \
          11  13  4
         /  \      \
        7    2      1

return true, as there exist a root-to-leaf path 5->4->11->2 which sum is 22.
```

路径和定义为从 root 到 leaf 的所有节点的和。

**题解：**

方法1：递归

```cpp
class Solution {
public:
    bool hasPathSum(TreeNode* root, int sum) {
        if(!root) return false;
        if(!root->left && !root->right && sum == root->val) return true;
        return hasPathSum(root->left, sum - root->val) || // 两子树有一棵存在路径即可
               hasPathSum(root->right, sum - root->val);
    }
};
```

方法2：DFS

```cpp
class Solution {
public:
    bool res = false;
    bool hasPathSum(TreeNode* root, int sum) {
        if(!root) return false;
        dfs(root, sum);
        return res;
    }

    void dfs(TreeNode* root, int sum) {
        if(!root) {
            return;
        }
        if(!root->left && !root->right) { // 叶子节点
            if(sum == root->val) res = true;
        }
        
        dfs(root->left, sum - root->val);
        dfs(root->right, sum - root->val);
    }
};
```



## 8. 统计路径和等于一个数的路径数量⭐️

437\. Path Sum III / 路径总和 III (Easy)

[Leetcode](https://leetcode.com/problems/path-sum-iii/description/) / [力扣](https://leetcode-cn.com/problems/path-sum-iii/description/)

```html
root = [10,5,-3,3,2,null,11,3,-2,null,1], sum = 8

      10
     /  \
    5   -3
   / \    \
  3   2   11
 / \   \
3  -2   1

Return 3. The paths that sum to 8 are:

1.  5 -> 3
2.  5 -> 2 -> 1
3. -3 -> 11
```

路径不一定以 root 开头，也不一定以 leaf 结尾，但是必须连续。

**题解：**

方法1：**双递归**，以每个节点为根节点，算一遍路径和为sum的有几条，然后加起来

```cpp
class Solution {
public:
    int pathSum(TreeNode* root, int sum) { // 递归遍历各个根节点，并统计各节点为根节点的子树
        if(!root) return 0;
        return helper(root, sum) + pathSum(root->left, sum) + pathSum(root->right, sum);
    }
    int helper(TreeNode* root, int sum) { // 递归统计以某节点为根节点的子树
        if(!root) return 0;
        sum -= root->val;
        return (sum == 0 ? 1 : 0) + helper(root->left, sum) + helper(root->right, sum);
    }
};
```

方法2：先序遍历每个节点，再对每个节点进行DFS（上题<路径总和>中的方法），搜索和为sum的路径

```cpp
class Solution {
public:
    int res = 0;
    int pathSum(TreeNode* root, int sum) {
        if(!root) return 0;
        // 先序遍历各个节点
        dfs(root, sum); // 在以每个节点为根节点的子树上搜索路径
        pathSum(root->left, sum);
        pathSum(root->right, sum);
        return res;
    }

    void dfs(TreeNode* root, int sum) {
        if(!root) {
            return;
        }
        if(sum == root->val) res++;
        
        dfs(root->left, sum - root->val);
        dfs(root->right, sum - root->val);
    }
};
```



## 9. 子树⭐️

572\. Subtree of Another Tree (Easy)

[Leetcode](https://leetcode.com/problems/subtree-of-another-tree/description/) / [力扣](https://leetcode-cn.com/problems/subtree-of-another-tree/description/)

```html
Given tree s:
     3
    / \
   4   5
  / \
 1   2

Given tree t:
   4
  / \
 1   2

Return true, because t has the same structure and node values with a subtree of s.

Given tree s:

     3
    / \
   4   5
  / \
 1   2
    /
   0

Given tree t:
   4
  / \
 1   2

Return false.
```

**题解：双递归**

辅助函数执行递归先序遍历，同时遍历两树的各个节点，判断两树是否相同（即各节点是否相等）。

树t为s的子树的条件（满足其一即可）：

- t就等于s本身
- t是s的左子树的子树
- t是s的右子树的子树

主函数执行递归先序遍历，判断以 s 树中当前节点开始的树是否与 t 相同、当前节点左子树是否与 t 相同、当前节点右子树是否与 t 相同，有一个相同则 t 为 s 的子树。

```cpp
class Solution {
public:
    bool isSubtree(TreeNode* s, TreeNode* t) {
        if(!s) return false;
        bool cur = isSametree(s, t); // 判断t是否为当前树相同
        bool left = isSubtree(s->left, t); // 判断t是否为与前节点左子树相同
        bool right = isSubtree(s->right, t); // 判断t是否为当前节点右子树相同
        return cur || left || right; // 当前树、左子树、右子树有一个与t相同则t为s的子树
    }

    bool isSametree(TreeNode* s, TreeNode* t) { // 判断s是否与t相同
        if(!s && !t) return true; // 根节点均为空，两树相同
        if(!s || !t) return false; // 一个根节点为空，另一个不空，两树不同

        if(s->val != t->val) return false; // 节点值不等，一定不同，否则继续判断左右子树

        bool left = isSametree(s->left, t->left);
        bool right = isSametree(s->right, t->right);
        return left && right; // 左右子树都相同，两树相同
    }
};
```

化简：

```cpp
class Solution {
public:
    bool isSubtree(TreeNode* s, TreeNode* t) {
        if(!s) return false;
        // 当前树、左子树、右子树有一个与t相同则t为s的子树
        return isSametree(s, t) || isSubtree(s->left, t) || isSubtree(s->right, t);
    }

    bool isSametree(TreeNode* s, TreeNode* t) { // 判断s是否与t相同
        if(!s && !t) return true; // 根节点均为空，两树相同
        if(!s || !t) return false; // 一个根节点为空，另一个不空，两树不同
        if(s->val != t->val) return false; // 节点值不等，一定不同，否则继续判断左右子树
        return isSametree(s->left, t->left) && isSametree(s->right, t->right); // 左右子树都相同，两树相同
    }
};
```



## 10. 树的对称

101\. Symmetric Tree (Easy)

[Leetcode](https://leetcode.com/problems/symmetric-tree/description/) / [力扣](https://leetcode-cn.com/problems/symmetric-tree/description/)

```html
    1
   / \
  2   2
 / \ / \
3  4 4  3
```

**题解：**

此题判断两个子树之间的关系，必然用到双根递归函数。此题类似于判断两棵树是否相等，相同点是两棵树的结构相同，不同点是相等的两棵树不仅结构相同并且节点值也相同。

可以发现，一棵树的先序遍历结果与其对称树的反向先序遍历结果相同。因而，同时对两树进行先序遍历，一棵正常先序遍历，一棵反向先序遍历，逐个判断遍历到的节点值是否相等即可判断两棵树是否互为镜像。

```cpp
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if(!root) return true;
        return isSymmetric(root->left, root->right);
    }

    bool isSymmetric(TreeNode* t1, TreeNode* t2) {
        if(!t1 && !t2) return true; // 同时为空，为对称
        if(!t1 || !t2) return false; // 一个为空，一个不空，一定不对称
        if(t1->val != t2->val) return false; // 节点值不相等，一定不对称，否则继续遍历
        bool left = isSymmetric(t1->left, t2->right); // 同时对左子树进行先序遍历
        bool right = isSymmetric(t1->right, t2->left); // 右子树进行反向先序遍历
        return left && right; // 左子树对称并且右子树对称，则此树对称
    }
};
```



## 11. 统计左叶子节点的和

404\. Sum of Left Leaves (Easy)

[Leetcode](https://leetcode.com/problems/sum-of-left-leaves/description/) / [力扣](https://leetcode-cn.com/problems/sum-of-left-leaves/description/)

```html
    3
   / \
  9  20
    /  \
   15   7

There are two left leaves in the binary tree, with values 9 and 15 respectively. Return 24.
```

**题解**：`if(!root->left && !root->right)`可以判断当前节点是否为叶子节点，但要判断某一节点是否是左子节点只能从其双亲节点入手，**当前双亲节点的左子节点存在且左子节点的左子节点及右子节点均不存在，则当前双亲节点的左子节点为叶子节点**，即`(root->left && !root->left->left && !root->left->right)`。

分三种情况递归计算个子树的左叶子和：

- 当前节点为空时，左叶子和为0

- 当前节点的左子节点为叶子节点时，当前节点所在子树的左叶子和为左叶子的值加上右子树的左叶子和，即`root->left->val + sumOfLeftLeaves(root->right)`
- 当前节点左子节点不为叶子节点时，当前节点所在子树的左叶子和为左子树的左叶子和加上右子树的左叶子和，即`sumOfLeftLeaves(root->left) + sumOfLeftLeaves(root->right)`

紧凑写法：

```cpp
class Solution {
public:
    int sumOfLeftLeaves(TreeNode* root) {
        if(!root) return 0;
        if(root->left && !root->left->left && !root->left->right) // 判断是否是左叶子节点
            return root->left->val + sumOfLeftLeaves(root->right);
        return sumOfLeftLeaves(root->left) + sumOfLeftLeaves(root->right);
    }
};
```

容易理解的写法：

```cpp
class Solution {
public:
    int sumOfLeftLeaves(TreeNode* root) {
        if(!root) return 0;

        int sum = 0;
        if (root->left && !root->left->left && !root->left->right) // 判断是否是左叶子节点
            sum += root->left->val;
        else
            sum += sumOfLeftLeaves(root->left);

        sum += sumOfLeftLeaves(root->right);
        return sum;
    }
};
```



## 12. 相同节点值的最大路径长度（较难）

687\. Longest Univalue Path / 最长相同值路径 (Easy)

[Leetcode](https://leetcode.com/problems/longest-univalue-path/) / [力扣](https://leetcode-cn.com/problems/longest-univalue-path/)

```html
             1
            / \
           4   5
          / \   \
         4   4   5

Output : 2
```

[**题解**](https://www.cnblogs.com/grandyang/p/7636259.html)：首先对其左右子结点调用递归函数，得到其左右子树的最大相同值路径长度，下面就要来看当前结点和其左右子结点之间的关系了，如果其左子结点存在且和当前节点值相同，则left自增1，否则left重置0；同理，如果其右子结点存在且和当前节点值相同，则right自增1，否则right重置0。然后用left+right来更新结果res。而调用当前节点值的函数只能返回left和right中的较大值，因为如果还要跟父节点组path，就只能在左右子节点中选一条path，当然选值大的那个了。

紧凑写法：

```cpp
class Solution {
public:
    int res = 0;
    int longestUnivaluePath(TreeNode* root) {
        if(!root) return 0;
        dfs(root);
        return res;
    }

    int dfs(TreeNode* root) {
        if(!root) return 0;

        int left = dfs(root->left);
        int right = dfs(root->right);

        left = root->left && root->left->val == root->val ? left + 1 : 0;
        right = root->right && root->right->val == root->val ? right + 1 : 0;

        res = max(res, left + right);
        return max(left, right);
    }
};
```

清晰写法：

```cpp
class Solution {
public:
    int res = 0;
    int longestUnivaluePath(TreeNode* root) {
        if(!root) return 0;
        dfs(root);
        return res;
    }

    int dfs(TreeNode* root) {
        if(!root) return 0;

        int left = dfs(root->left);
        int right = dfs(root->right);

        int leftLen = 0, rightLen = 0;
        if(root->left && root->left->val == root->val) { // 与左子节点值相等
            leftLen = left + 1;
        }
            
        if(root->right && root->right->val == root->val) { // 与右子节点值相等
            rightLen = right + 1;
        }

        res = max(res, leftLen + rightLen);
        return max(leftLen, rightLen);
    }
};
```



## 13. 间隔遍历

337\. House Robber III / 打家劫舍III (Medium)

[Leetcode](https://leetcode.com/problems/house-robber-iii/description/) / [力扣](https://leetcode-cn.com/problems/house-robber-iii/description/)

```html
     3
    / \
   2   3
    \   \
     3   1
Maximum amount of money the thief can rob = 3 + 3 + 1 = 7.
```

**题解**：

1、超时解法：

爷爷节点获取到最大的偷取的钱数：

- 首先要明确相邻的节点不能偷，也就是爷爷选择偷，儿子就不能偷了，但是孙子可以偷
- 二叉树只有左右两个孩子，一个爷爷最多 2 个儿子，4 个孙子

根据以上条件，我们可以得出单个节点的钱该怎么算：
4 个孙子偷的钱 + 爷爷的钱 VS 两个儿子偷的钱 哪个组合钱多，就当做当前节点能偷的最大钱数。这就是动态规划里面的最优子结构

由于是二叉树，这里可以选择计算所有子节点

4 个孙子偷的钱加上爷爷的钱如下：
`int method1 = root.val + rob(root->left->left) + rob(root->left->right) + rob(root->right->left) + rob(root->right->right);`
两个儿子偷的钱如下
`int method2 = rob(root->left) + rob(root->right);`
挑选一个钱数多的方案则
`int result = max(method1, method2);`

```cpp
class Solution {
public:
    int rob(TreeNode* root) {
        if(!root) return 0;
        int val = root->val;
        if(root->left) val += (rob(root->left->left) + rob(root->left->right));
        if(root->right) val += (rob(root->right->left) + rob(root->right->right));
        return max(val, rob(root->left) + rob(root->right));
    }
};
```

2、结合动态规划：

```cpp

```



## 14. 找出二叉树中第二小的节点

671\. Second Minimum Node In a Binary Tree / 二叉树中第二小的节点 (Easy)

[Leetcode](https://leetcode.com/problems/second-minimum-node-in-a-binary-tree/description/) / [力扣](https://leetcode-cn.com/problems/second-minimum-node-in-a-binary-tree/description/)

```html
Input:
   2
  / \
 2   5
    / \
    5  7

Output: 5
```

一个节点要么具有 0 个或 2 个子节点，如果有子节点，那么根节点是最小的节点。

```cpp
public int findSecondMinimumValue(TreeNode root) {
    if (root == null) return -1;
    if (root.left == null && root.right == null) return -1;
    int leftVal = root.left.val;
    int rightVal = root.right.val;
    if (leftVal == root.val) leftVal = findSecondMinimumValue(root.left);
    if (rightVal == root.val) rightVal = findSecondMinimumValue(root.right);
    if (leftVal != -1 && rightVal != -1) return Math.min(leftVal, rightVal);
    if (leftVal != -1) return leftVal;
    return rightVal;
}
```



# 层次遍历

使用 BFS 进行层次遍历。不需要使用两个队列来分别存储当前层的节点和下一层的节点，因为在开始遍历一层的节点时，当前队列中的节点数就是当前层的节点数，只要控制遍历这么多节点数，就能保证这次遍历的都是当前层的节点。



## 1. 二叉树的层序遍历

[102. 二叉树的层序遍历 (Medium)](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

**题解**：使用队列

```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if(!root) return res;

        queue<TreeNode*> qu;
        qu.push(root);

        while(!qu.empty()) {
            int cnt = qu.size();
            vector<int> vec;
            while(cnt--) {
                auto t = qu.front(); qu.pop();
                vec.push_back(t->val);
                if(t->left) qu.push(t->left);
                if(t->right) qu.push(t->right);
            }
            if(!vec.empty()) res.push_back(vec);
        }
        return res;
    }
};
```



## 2. 一棵树每层节点的平均数

637\. Average of Levels in Binary Tree (Easy)

[Leetcode](https://leetcode.com/problems/average-of-levels-in-binary-tree/description/) / [力扣](https://leetcode-cn.com/problems/average-of-levels-in-binary-tree/description/)

```cpp
class Solution {
public:
    vector<double> averageOfLevels(TreeNode* root) {
        vector<double> res;
        if(!root) return res;

        queue<TreeNode*> qu;
        qu.push(root);

        while(!qu.empty()) {
            int cnt = qu.size();
            long double sum = 0;
            for(int i = 0; i < cnt; i++) {
                auto t = qu.front(); qu.pop();
                sum += t->val;
                if(t->left) qu.push(t->left);
                if(t->right) qu.push(t->right);
            }
            res.push_back(sum/cnt);
        }
        return res;
    }
};
```



## 3. 得到左下角的节点

513\. Find Bottom Left Tree Value (Easy)

[Leetcode](https://leetcode.com/problems/find-bottom-left-tree-value/description/) / [力扣](https://leetcode-cn.com/problems/find-bottom-left-tree-value/description/)

```html
Input:

        1
       / \
      2   3
     /   / \
    4   5   6
       /
      7

Output:
7
```

**题解**：

方法1：先添加右子节点再添加左子节点，即可保证最后一个出队的为最底层的最左侧节点

```cpp
class Solution {
public:
    int findBottomLeftValue(TreeNode* root) {
        queue<TreeNode*> qu;
        qu.push(root);

        vector<int> vec;
        while(!qu.empty()) {
            root = qu.front(); qu.pop();
            if(root->right) qu.push(root->right);
            if(root->left) qu.push(root->left);
        }
        return root->val;
    }
};
```

方法2：设置一个变量来保存每层的最左侧节点，最后此变量保存的即为最后一层的最左侧节点

```cpp
class Solution {
public:
    int findBottomLeftValue(TreeNode* root) {
        queue<TreeNode*> qu;
        qu.push(root);
        int res = 0;

        vector<int> vec;
        while(!qu.empty()) {
            int cnt = qu.size();
            for(int i = 0; i < cnt; i++) {
                auto t = qu.front(); qu.pop();
                if(i == 0) res =  t->val; // 保存每行第一个
                if(t->left) qu.push(t->left);
                if(t->right) qu.push(t->right);
            }
        }
        return res;
    }
};
```



## 4. 锯齿形层次遍历

[Leetcode 103. 二叉树的锯齿形层次遍历](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

**题解**：

方法1：设置标志位，奇数层翻转结果数组

```cpp
class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if(!root) return res;
        bool flag = false;
        queue<TreeNode*> qu;
        qu.push(root);

        while(!qu.empty()) {
            int cnt = qu.size();
            vector<int> vec;
            while(cnt--) {
                auto t = qu.front(); qu.pop();
                vec.push_back(t->val);
                if(t->left) qu.push(t->left);
                if(t->right) qu.push(t->right);
            }
            if(flag) reverse(vec.begin(), vec.end());
            if(!vec.empty()) res.push_back(vec);
            flag = !flag;
        }
        return res;
    }
};
```



方法2：使用双端队列

```cpp
class Solution {
public:
    // 简单方法，使用deque，为了区分打印顺序，使用deque而不是queue
    vector<vector<int>> zigzagLevelOrder(TreeNode* pRoot) {
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



# 前中后序遍历

```html
    1
   / \
  2   3
 / \   \
4   5   6
```

- 层次遍历顺序：[1 2 3 4 5 6]
- 前序遍历顺序：[1 2 4 5 3 6]
- 中序遍历顺序：[4 2 5 1 3 6]
- 后序遍历顺序：[4 5 2 6 3 1]

层次遍历使用 BFS 实现，利用的就是 BFS 一层一层遍历的特性；而前序、中序、后序遍历利用了 DFS 实现。

前序、中序、后序遍只是在对节点访问的顺序有一点不同，其它都相同。

① 前序

```cpp
void dfs(TreeNode root) {
    visit(root);
    dfs(root.left);
    dfs(root.right);
}
```

② 中序

```cpp
void dfs(TreeNode root) {
    dfs(root.left);
    visit(root);
    dfs(root.right);
}
```

③ 后序

```cpp
void dfs(TreeNode root) {
    dfs(root.left);
    dfs(root.right);
    visit(root);
}
```



## 1. 非递归实现二叉树的前序遍历

144\. Binary Tree Preorder Traversal / 二叉树的前序遍历 (Medium)

[Leetcode](https://leetcode.com/problems/binary-tree-preorder-traversal/description/) / [力扣](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/description/)

**题解：**从根节点开始，每次迭代弹出当前栈顶元素，并将其孩子节点压入栈中，先压右孩子再压左孩子。

迭代写法1：

```cpp
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> res;
        stack<TreeNode*> stk;
        
        stk.push(root);
        while(!stk.empty()) {
            auto node = stk.top(); stk.pop();
            if(!node) continue;

            res.push_back(node->val);
            stk.push(node->right); // 先右后左，保证左子树先遍历
            stk.push(node->left);
        }
        return res;
    }
};
```

迭代写法2：

```cpp
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> res;
        stack<TreeNode*> stk;

        if(!root) return res;
        
        stk.push(root);
        while(!stk.empty()) {
            auto node = stk.top(); stk.pop();

            res.push_back(node->val);
            if(node->right) stk.push(node->right); // 先右后左，保证左子树先遍历
            if(node->left) stk.push(node->left);
        }
        return res;
    }
};
```

递归写法：

```cpp
class Solution {
public:
    vector<int> res;
    vector<int> preorderTraversal(TreeNode* root) {
        if(!root) return res;

        res.push_back(root->val);
        preorderTraversal(root->left);
        preorderTraversal(root->right);
        return res;
    }
};
```



## 2. 非递归实现二叉树的后序遍历

145\. Binary Tree Postorder Traversal / 二叉树的后序遍历 (Medium)

[Leetcode](https://leetcode.com/problems/binary-tree-postorder-traversal/description/) / [力扣](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/description/)

前序遍历为 root -> left -> right，后序遍历为 left -> right -> root。可以修改前序遍历成为 root -> right -> left，那么这个顺序就和后序遍历正好相反。

使用栈遍历二叉树，先入栈的为根节点，控制左右节点的入栈顺序可实现root -> left -> right 和 root -> right -> left 两种遍历顺序，即先序遍历和反向先序遍历

迭代写法：

```cpp
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> res;
        stack<TreeNode*> stk;
        
        stk.push(root);
        while(!stk.empty()) {
            auto node = stk.top(); stk.pop();
            if(!node) continue;

            res.push_back(node->val);
            stk.push(node->left); // 先左后右，保证右子树先遍历，实现反向先序遍历
            stk.push(node->right);
        }
        reverse(res.begin(), res.end()); // 反向先序遍历的结果为后序遍历
        return res;
    }
};
```

递归写法：

```cpp
class Solution {
public:
    vector<int> res;
    vector<int> postorderTraversal(TreeNode* root) {
        if(!root) return res;

        postorderTraversal(root->left);
        postorderTraversal(root->right);
        res.push_back(root->val);
        return res;
    }
};
```



## 3. 非递归实现二叉树的中序遍历⭐️

94\. Binary Tree Inorder Traversal / 二叉树的中序遍历 (Medium)

[Leetcode](https://leetcode.com/problems/binary-tree-inorder-traversal/description/) / [力扣](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/description/)

**[题解](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/solution/dong-hua-yan-shi-94-er-cha-shu-de-zhong-xu-bian-li/)：**

递归实现时，是函数自己调用自己，一层层的嵌套下去，操作系统/虚拟机自动帮我们用栈来保存了每个调用的函数，现在我们需要自己模拟这样的调用过程。
递归的调用过程是这样的：

```
dfs(root.left)
	dfs(root.left)
		dfs(root.left)
			为null返回
		打印节点
		dfs(root.right)
			dfs(root.left)
				dfs(root.left)
				........
```

递归的调用过程是不断往左边走，当左边走不下去了，就打印节点，并转向右边，然后右边继续这个过程。
我们在迭代实现时，就可以用栈来模拟上面的调用过程。

<img src="https://pic.leetcode-cn.com/47fff35dd3fd640ba60349c78b85242ae8f4b850f06a282cd7e92c91e6eff406-1.gif" alt="1.gif" style="zoom: 67%;" />

迭代写法1：

```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        stack<TreeNode*> stk;

        TreeNode* cur = root;
        while(cur || !stk.empty()) {
            if(cur) { // 不断往左子树方向走，将当前节点保存到栈中，模拟递归调用
                stk.push(cur);
                cur = cur->left; // 往左边走
            } else { // 当前节点为空，说明到达左子树叶子节点，逐个弹出节点并保存，然后转向右边节点
                TreeNode* node = stk.top(); stk.pop();
                res.push_back(node->val);
                cur = node->right; // 转向右边
            }
        }
        return res;
    }
};
```

迭代写法2：

```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        stack<TreeNode*> stk;

        TreeNode* cur = root;
        while(cur || !stk.empty()) {
            while(cur) {
                stk.push(cur);
                cur = cur->left;
            }

            TreeNode* node = stk.top(); stk.pop();
            res.push_back(node->val);
            cur = node->right;
        }
        return res;
    }
};
```

递归写法：

```cpp
class Solution {
public:
    vector<int> res;
    vector<int> inorderTraversal(TreeNode* root) {
        if(!root) return res;

        inorderTraversal(root->left);
        res.push_back(root->val);
        inorderTraversal(root->right);
        return res;
    }
};
```



# BST

二叉查找树（BST）：根节点大于等于左子树所有节点，小于等于右子树所有节点。

二叉查找树中序遍历有序。

## 1. 修剪二叉查找树

669\. Trim a Binary Search Tree / 修剪二叉搜索树 (Easy)

[Leetcode](https://leetcode.com/problems/trim-a-binary-search-tree/description/) / [力扣](https://leetcode-cn.com/problems/trim-a-binary-search-tree/description/)

```html
Input:

    3
   / \
  0   4
   \
    2
   /
  1

  L = 1
  R = 3

Output:

      3
     /
   2
  /
 1
```

题目描述：只保留值在 L \~ R 之间的节点

**题解**：

当node.val > R，那么修剪后的二叉树必定出现在节点的左边，因为此节点需要删除，并且比R小的节点只可能在左边。同理当node.val < L，那么修剪后的二叉树出现在节点的右边。否则，我们将会修剪树的两边。

```cpp
class Solution {
public:
    TreeNode* trimBST(TreeNode* root, int L, int R) {
        if(!root) return root;

        if(root->val > R) return trimBST(root->left, L, R); // 删除当前节点及右子树
        if(root->val < L) return trimBST(root->right, L, R); // 删除当前节点及左子树
        
        root->left = trimBST(root->left, L, R); // 修剪左子树
        root->right = trimBST(root->right, L, R); // 修剪右子树
        return root;
    }
};
```



## 2. 寻找二叉查找树的第 k 个元素

230\. Kth Smallest Element in a BST / 二叉搜索树中第K小的元素 (Medium)

[Leetcode](https://leetcode.com/problems/kth-smallest-element-in-a-bst/description/) / [力扣](https://leetcode-cn.com/problems/kth-smallest-element-in-a-bst/description/)

**题解**：


BST中序遍历的结果是有序的，存储结果并取出第k个元素即可

```cpp
class Solution {
public:
    vector<int> vec;
    int kthSmallest(TreeNode* root, int k) {
        if(!root) return 0;
        inOrder(root);
        return vec[k - 1];
    }

    void inOrder(TreeNode* root) {
        if(!root) return;
        inOrder(root->left);
        vec.push_back(root->val);
        inOrder(root->right);
    }
}
```



## 3. 把二叉查找树每个节点的值都加上比它大的节点的值

538\. Convert BST to Greater Tree / 把二叉搜索树转换为累加树 (Easy)

[Leetcode](https://leetcode.com/problems/convert-bst-to-greater-tree/description/) / [力扣](https://leetcode-cn.com/problems/convert-bst-to-greater-tree/description/)

```html
Input: The root of a Binary Search Tree like this:

              5
            /   \
           2     13

Output: The root of a Greater Tree like this:

             18
            /   \
          20     13
```

**题解**：使用**反向中序遍历**，将中序遍历左根右的顺序逆过来，变成右根左的顺序，这样就可以反向计算累加和sum，同时更新结点值。例如按13->5->2的顺序遍历，sum+=13，此时sum = 13，为右子节点更新后的值。sum+=5，此时sum=18，为根节点更新后的值。sum+=2，此时sum=20，为左子节点更新后的值。

```cpp
class Solution {
public:
    int sum = 0;
    TreeNode* convertBST(TreeNode* root) {
        if(!root) return NULL;
        convertBST(root->right);
        sum += root->val; // 累加计算当前节点的值
        root->val = sum;
        convertBST(root->left);
        return root;
    }
};
```



## 4. 二叉查找树的最近公共祖先⭐️

235\. Lowest Common Ancestor of a Binary Search Tree / 二叉搜索树的最近公共祖先 (Easy)

[Leetcode](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/description/) / [力扣](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree/description/)

```html
        _______6______
      /                \
  ___2__             ___8__
 /      \           /      \
0        4         7        9
        /  \
       3   5

For example, the lowest common ancestor (LCA) of nodes 2 and 8 is 6. Another example is LCA of nodes 2 and 4 is 2, since a node can be a descendant of itself according to the LCA definition.
```

**题解**：

由于二叉搜索树的特点是左<根<右，所以根节点的值一直都是中间值，大于左子树的所有节点值，小于右子树的所有节点值，那么我们可以做如下的判断：

如果根节点的值大于p和q之间的较大值，说明p和q都在左子树中，那么此时我们就进入根节点的左子节点继续递归，如果根节点小于p和q之间的较小值，说明p和q都在右子树中，那么此时我们就进入根节点的右子节点继续递归，如果都不是，则说明当前根节点就是最小共同父节点，直接返回即可。

如下图所示，节点0和5都比节点6小，因而其公共祖先必在节点6的左子树；而节点0比节点2小，节点5比节点2大，说明节点2即为节点0和5的最近公共祖先

<img src="https://gitee.com//MrRen-sdhm/Images/raw/master/img/20200522171044.png" alt="image-20200522171032834" style="zoom: 80%;" />

```cpp
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(!root) return root;
        if(root->val > p->val && root->val > q->val) return lowestCommonAncestor(root->left, p, q);
        if(root->val < p->val && root->val < q->val) return lowestCommonAncestor(root->right, p, q);
        return root;
    }
};
```



## 5. 二叉树的最近公共祖先⭐️

236\. Lowest Common Ancestor of a Binary Tree / 二叉树的最近公共祖先 (Medium) 

[Leetcode](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/description/) / [力扣](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/description/)

```html
       _______3______
      /              \
  ___5__           ___1__
 /      \         /      \
6        2       0        8
        /  \
       7    4

For example, the lowest common ancestor (LCA) of nodes 5 and 1 is 3. 
Another example is LCA of nodes 5 and 4 is 5, since a node can be a descendant of itself according to the LCA definition.
```

**题解**：

这道题是普通是二叉树，不是二叉搜索树，所以就不能利用其特有的性质，我们只能在二叉树中来搜索p和q，然后从路径中找到最后一个相同的节点即为父节点，可以用递归来实现，在递归函数中，**首先看当前结点是否为空，若为空则直接返回空，若为p或q中的任意一个，也直接返回当前结点**。否则的话就对其左右子结点分别调用递归函数，由于这道题限制了p和q一定都在二叉树中存在，那么**如果当前结点不等于p或q，p和q要么分别位于左右子树中，要么同时位于左子树，或者同时位于右子树**，那么我们分别来讨论：

- 若p和q分别位于左右子树中，那么对左右子结点调用递归函数，会分别返回p和q结点的位置，而当前结点正好就是p和q的最小共同父结点，直接返回当前结点即可，这就是题目中的例子1的情况。

- 若p和q同时位于左子树，这里有两种情况，一种情况是 left 会返回p和q中较高的那个位置，而 right 会返回空，所以最终返回非空的 left 即可，这就是题目中的例子2的情况。还有一种情况是会返回p和q的最小父结点，就是说当前结点的左子树中的某个结点才是p和q的最小父结点，会被返回。

- 若p和q同时位于右子树，同样这里有两种情况，一种情况是 right 会返回p和q中较高的那个位置，而 left 会返回空，所以最终返回非空的 right 即可，还有一种情况是会返回p和q的最小父结点，就是说当前结点的右子树中的某个结点才是p和q的最小父结点，会被返回。

```cpp
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(!root || root == p || root == q) return root;

        TreeNode *left = lowestCommonAncestor(root->left, p, q);
        TreeNode *right = lowestCommonAncestor(root->right, p, q);
        if(left && right) return root; // pq位于当前节点两侧
        return left ? left : right; // pq位于当前节点同一侧，返回非空的节点
    }
};
```



## 6. 从有序数组中构造二叉查找树

108\. Convert Sorted Array to Binary Search Tree / 将有序数组转换为二叉搜索树 (Easy)

[Leetcode](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/description/) / [力扣](https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree/description/)

**题解**：

二叉搜索树，是一种始终满足左<根<右的特性，如果将**二叉搜索树按中序遍历的话，得到的就是一个有序数组**。那么反过来，我们可以得知，**根节点应该是有序数组的中间点**，从中间点分开为左右两个有序数组，在分别找出其中间点作为原中间点的左右两个子节点，这不就是二分查找么。

```cpp
class Solution {
public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        return toBST(nums, 0, nums.size() - 1);
    }

    TreeNode* toBST(vector<int>& nums, int l, int r) {
        if(l > r) return NULL;
        int mid = (l + r) >> 1;
        TreeNode *root = new TreeNode(nums[mid]);
        root->left = toBST(nums, l, mid - 1);
        root->right = toBST(nums, mid + 1, r);
        return root;
    }
};
```



## 7. 根据有序链表构造平衡的二叉查找树✏️

109\. Convert Sorted List to Binary Search Tree / 有序链表转换二叉搜索树 (Medium)

[Leetcode](https://leetcode.com/problems/convert-sorted-list-to-binary-search-tree/description/) / [力扣](https://leetcode-cn.com/problems/convert-sorted-list-to-binary-search-tree/description/)

```html
Given the sorted linked list: [-10,-3,0,5,9],

One possible answer is: [0,-3,9,-10,null,5], which represents the following height balanced BST:

      0
     / \
   -3   9
   /   /
 -10  5
```

**题解**：

```cpp
public TreeNode sortedListToBST(ListNode head) {
    if (head == null) return null;
    if (head.next == null) return new TreeNode(head.val);
    ListNode preMid = preMid(head);
    ListNode mid = preMid.next;
    preMid.next = null;  // 断开链表
    TreeNode t = new TreeNode(mid.val);
    t.left = sortedListToBST(head);
    t.right = sortedListToBST(mid.next);
    return t;
}

private ListNode preMid(ListNode head) {
    ListNode slow = head, fast = head.next;
    ListNode pre = head;
    while (fast != null && fast.next != null) {
        pre = slow;
        slow = slow.next;
        fast = fast.next.next;
    }
    return pre;
}
```



## 8. 在二叉查找树中寻找两个节点，使它们的和为一个给定值

653\. Two Sum IV - Input is a BST (Easy)

[Leetcode](https://leetcode.com/problems/two-sum-iv-input-is-a-bst/description/) / [力扣](https://leetcode-cn.com/problems/two-sum-iv-input-is-a-bst/description/)

```html
Input:

    5
   / \
  3   6
 / \   \
2   4   7

Target = 9

Output: True
```

使用中序遍历得到有序数组之后，再利用双指针对数组进行查找。

应该注意到，这一题不能用分别在左右子树两部分来处理这种思想，因为两个待求的节点可能分别在左右子树中。

```cpp
public boolean findTarget(TreeNode root, int k) {
    List<Integer> nums = new ArrayList<>();
    inOrder(root, nums);
    int i = 0, j = nums.size() - 1;
    while (i < j) {
        int sum = nums.get(i) + nums.get(j);
        if (sum == k) return true;
        if (sum < k) i++;
        else j--;
    }
    return false;
}

private void inOrder(TreeNode root, List<Integer> nums) {
    if (root == null) return;
    inOrder(root.left, nums);
    nums.add(root.val);
    inOrder(root.right, nums);
}
```



## 9. 在二叉查找树中查找两个节点之差的最小绝对值

530\. Minimum Absolute Difference in BST (Easy)

[Leetcode](https://leetcode.com/problems/minimum-absolute-difference-in-bst/description/) / [力扣](https://leetcode-cn.com/problems/minimum-absolute-difference-in-bst/description/)

```html
Input:

   1
    \
     3
    /
   2

Output:

1
```

利用二叉查找树的中序遍历为有序的性质，计算中序遍历中临近的两个节点之差的绝对值，取最小值。

```cpp
private int minDiff = Integer.MAX_VALUE;
private TreeNode preNode = null;

public int getMinimumDifference(TreeNode root) {
    inOrder(root);
    return minDiff;
}

private void inOrder(TreeNode node) {
    if (node == null) return;
    inOrder(node.left);
    if (preNode != null) minDiff = Math.min(minDiff, node.val - preNode.val);
    preNode = node;
    inOrder(node.right);
}
```



## 10. 寻找二叉查找树中出现次数最多的值

501\. Find Mode in Binary Search Tree (Easy)

[Leetcode](https://leetcode.com/problems/find-mode-in-binary-search-tree/description/) / [力扣](https://leetcode-cn.com/problems/find-mode-in-binary-search-tree/description/)

```html
   1
    \
     2
    /
   2

return [2].
```

答案可能不止一个，也就是有多个值出现的次数一样多。

```cpp
private int curCnt = 1;
private int maxCnt = 1;
private TreeNode preNode = null;

public int[] findMode(TreeNode root) {
    List<Integer> maxCntNums = new ArrayList<>();
    inOrder(root, maxCntNums);
    int[] ret = new int[maxCntNums.size()];
    int idx = 0;
    for (int num : maxCntNums) {
        ret[idx++] = num;
    }
    return ret;
}

private void inOrder(TreeNode node, List<Integer> nums) {
    if (node == null) return;
    inOrder(node.left, nums);
    if (preNode != null) {
        if (preNode.val == node.val) curCnt++;
        else curCnt = 1;
    }
    if (curCnt > maxCnt) {
        maxCnt = curCnt;
        nums.clear();
        nums.add(node.val);
    } else if (curCnt == maxCnt) {
        nums.add(node.val);
    }
    preNode = node;
    inOrder(node.right, nums);
}
```



# Trie

<div align="center"> <img src="https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/5c638d59-d4ae-4ba4-ad44-80bdc30f38dd.jpg"/> </div><br>

Trie，又称前缀树或字典树，用于判断字符串是否存在或者是否具有某种字符串前缀。



## 1. 实现一个 Trie

208\. Implement Trie (Prefix Tree) (Medium)

[Leetcode](https://leetcode.com/problems/implement-trie-prefix-tree/description/) / [力扣](https://leetcode-cn.com/problems/implement-trie-prefix-tree/description/)

```cpp
class Trie {

    private class Node {
        Node[] childs = new Node[26];
        boolean isLeaf;
    }

    private Node root = new Node();

    public Trie() {
    }

    public void insert(String word) {
        insert(word, root);
    }

    private void insert(String word, Node node) {
        if (node == null) return;
        if (word.length() == 0) {
            node.isLeaf = true;
            return;
        }
        int index = indexForChar(word.charAt(0));
        if (node.childs[index] == null) {
            node.childs[index] = new Node();
        }
        insert(word.substring(1), node.childs[index]);
    }

    public boolean search(String word) {
        return search(word, root);
    }

    private boolean search(String word, Node node) {
        if (node == null) return false;
        if (word.length() == 0) return node.isLeaf;
        int index = indexForChar(word.charAt(0));
        return search(word.substring(1), node.childs[index]);
    }

    public boolean startsWith(String prefix) {
        return startWith(prefix, root);
    }

    private boolean startWith(String prefix, Node node) {
        if (node == null) return false;
        if (prefix.length() == 0) return true;
        int index = indexForChar(prefix.charAt(0));
        return startWith(prefix.substring(1), node.childs[index]);
    }

    private int indexForChar(char c) {
        return c - 'a';
    }
}
```



## 2. 实现一个 Trie，用来求前缀和

677\. Map Sum Pairs (Medium)

[Leetcode](https://leetcode.com/problems/map-sum-pairs/description/) / [力扣](https://leetcode-cn.com/problems/map-sum-pairs/description/)

```html
Input: insert("apple", 3), Output: Null
Input: sum("ap"), Output: 3
Input: insert("app", 2), Output: Null
Input: sum("ap"), Output: 5
```

```cpp
class MapSum {

    private class Node {
        Node[] child = new Node[26];
        int value;
    }

    private Node root = new Node();

    public MapSum() {

    }

    public void insert(String key, int val) {
        insert(key, root, val);
    }

    private void insert(String key, Node node, int val) {
        if (node == null) return;
        if (key.length() == 0) {
            node.value = val;
            return;
        }
        int index = indexForChar(key.charAt(0));
        if (node.child[index] == null) {
            node.child[index] = new Node();
        }
        insert(key.substring(1), node.child[index], val);
    }

    public int sum(String prefix) {
        return sum(prefix, root);
    }

    private int sum(String prefix, Node node) {
        if (node == null) return 0;
        if (prefix.length() != 0) {
            int index = indexForChar(prefix.charAt(0));
            return sum(prefix.substring(1), node.child[index]);
        }
        int sum = node.value;
        for (Node child : node.child) {
            sum += sum(prefix, child);
        }
        return sum;
    }

    private int indexForChar(char c) {
        return c - 'a';
    }
}
```
