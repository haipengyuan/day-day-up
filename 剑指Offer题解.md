# [剑指 Offer 04. 二维数组中的查找](https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/)

## 同 [240. 搜索二维矩阵 II](https://leetcode-cn.com/problems/search-a-2d-matrix-ii/)

在一个 n * m 的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个高效的函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

**示例:**

现有矩阵 matrix 如下：

```
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
```

给定 target = `5`，返回 `true`。

给定 target = `20`，返回 `false`。

**限制：**

```
0 <= n <= 1000
0 <= m <= 1000
```

## 思路

由于给定的二维数组具备每行从**左到右递增**以及每列从**上到下递增**的特点，因此从二维数组的**右上角**开始查找。

* 如果当前元素等于目标值，则返回 `true` 。
* 如果当前元素大于目标值，说明当前元素的下边的所有元素都一定大于目标值，因此往下查找不可能找到目标值，往左查找可能找到目标值。
* 如果当前元素小于目标值，说明当前元素的左边的所有元素都一定小于目标值，因此往左查找不可能找到目标值，往下查找可能找到目标值。

```java
class Solution {
    public boolean findNumberIn2DArray(int[][] matrix, int target) {
        boolean flag = false;
        if (matrix != null &&  matrix.length > 0 && matrix[0].length > 0) {
            int n = 0;
            int m = matrix[0].length - 1;
            while (n < matrix.length && m >= 0) {
                if (target == matrix[n][m]) {
                    flag = true;
                    break;
                } else if (target > matrix[n][m]) {
                    n++;
                } else {
                    m--;
                }
            }
        }
        return flag;
    }
}
```

# [剑指 Offer 06. 从尾到头打印链表](https://leetcode-cn.com/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/)

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

**示例 1：**

```
输入：head = [1,3,2]
输出：[2,3,1]
```

**限制：**

0 <= 链表长度 <= 10000

## 栈

栈的特点为 **先进后出** ，因此将链表节点依次入栈，再出栈，即可从尾到头输出链表的节点的值。

```java
class Solution {
    public int[] reversePrint(ListNode head) {
        Deque<Integer> stack = new LinkedList<>();
        ListNode curr = head;
        while (curr != null) {
            stack.push(curr.val);
            curr = curr.next;
        }
        int[] res = new int[stack.size()];
        for (int i = 0; i < res.length; i++) {
            res[i] = stack.pop();
        }
        return res;
    }
}
```

## 递归（回溯）

使用递归，先走至链表尾，指针指向空时开始回溯，在回溯时依次保存头结点的值，即可反向输出链表的值。

```java
class Solution {
    List<Integer> tmp = new ArrayList<>();
    public int[] reversePrint(ListNode head) {
        recur(head);
        int[] res = new int[tmp.size()];
        for (int i = 0; i < res.length; i++) {
            res[i] = tmp.get(i);
        }
        return res;
    }

    private void recur(ListNode head) {
        if (head == null) return;	// 指向空时向上回溯
        recur(head.next);	// 向下递归
        tmp.add(head.val);
    }
}
```

* 时间复杂度：O(n)
* 空间复杂度：O(n) 

# [剑指 Offer 07. 重建二叉树](https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof/)

## 同 [105. 从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

输入某二叉树的前序遍历和中序遍历的结果，请重建该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。

例如，给出

```
前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]
```

返回如下的二叉树：

```
    3
   / \
  9  20
    /  \
   15   7
```

**限制：**

* 0 <= 节点个数 <= 5000

## 递归

```
前序遍历：[ 根节点, [左子树的前序遍历结果], [右子树的前序遍历结果] ]
中序遍历：[ [左子树的中序遍历结果], 根节点, [右子树的中序遍历结果] ]
```

即根节点总是前序遍历中的第一个节点。

在中序遍历中找到根节点的位置，就可以得到左右子树的节点数量以及左右子树的前序和中序遍历结果。

利用递归构造出左右子树，并接到根节点的左右位置。

* 在中序遍历中查找根节点的位置时，可以利用哈希表来快速定位（O(1)）。哈希表的键为节点的值，值为节点在中序遍历中的位置。

```java
class Solution {

    private Map<Integer, Integer> indexMap;

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        if (preorder == null || inorder == null || preorder.length <= 0 || inorder.length <= 0) return null;
        indexMap = new HashMap<Integer, Integer>();
        // 构造哈希表，快速查找根节点位置
        for (int i = 0; i < inorder.length; i++) {
            indexMap.put(inorder[i], i);
        }
        return myBuildTree(preorder, inorder, 0, preorder.length - 1, 0, inorder.length - 1);
    }

    // 递归构造二叉树的方法
    private TreeNode myBuildTree(int[] preorder, int[] inorder, int preorder_left, int preorder_right, int inorder_left, int inorder_right) {
        if (preorder_left > preorder_right) {
            return null;
        }

        // 前序遍历中的第一个节点为根节点
        int preorder_root = preorder_left;
        // 获取中序遍历中根节点的位置
        int inorder_root = indexMap.get(preorder[preorder_root]);
        TreeNode root = new TreeNode(preorder[preorder_root]);
        // 左子树节点个数
        int size_left = inorder_root - inorder_left;
        
        root.left = myBuildTree(preorder, inorder, preorder_left + 1, preorder_left + size_left, inorder_left, inorder_root - 1);
        root.right = myBuildTree(preorder, inorder, preorder_left + size_left + 1, preorder_right, inorder_root + 1, inorder_right);
        return root;
    }
}

/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
```

# [剑指 Offer 09. 用两个栈实现队列](https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/)

用两个栈实现一个队列。队列的声明如下，请实现它的两个函数 appendTail 和 deleteHead ，分别完成在队列尾部插入整数和在队列头部删除整数的功能。(若队列中没有元素，deleteHead 操作返回 -1 )

**示例 1：**

```
输入：
["CQueue","appendTail","deleteHead","deleteHead"]
[[],[3],[],[]]
输出：[null,null,3,-1]
```

**示例 2：**

```
输入：
["CQueue","deleteHead","appendTail","appendTail","deleteHead","deleteHead"]
[[],[],[5],[2],[],[]]
输出：[null,-1,null,null,5,2]
```

** **

* 1 <= values <= 10000
* 最多会对 appendTail、deleteHead 进行 10000 次调用

## 思路

插入元素时，直接插入到stack1中。

删除元素时，检查stack2是否为空？

* stack2为空，将stack1中的元素出栈，压入stack2中，此时stack2的栈顶元素为最早插入的元素，即要删除的元素。
* stack2不空，则直接删除stack2的栈顶元素。

```java
class CQueue {

    Deque<Integer> stack1;
    Deque<Integer> stack2;      // stack2栈顶为待删除的元素

    public CQueue() {
        stack1 = new LinkedList<Integer>();
        stack2 = new LinkedList<Integer>();
    }
    
    public void appendTail(int value) {
        stack1.push(value);
    }
    
    public int deleteHead() {
        if (stack2.isEmpty()) {
            while (!stack1.isEmpty()) {
                stack2.push(stack1.pop());
            }
        }
        return stack2.isEmpty() ? -1 : stack2.pop();
    }
}
```

* 时间复杂度：插入和删除均为O(1)，删除时，虽然要搬移数据，但每个元素只会「至多被插入和弹出 `stack2` 一次」，因此均摊下来仍为O(1)。
* 空间复杂度：O(n)

# [剑指 Offer 10- I. 斐波那契数列](https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/)

## 解法相同 [剑指 Offer 10- II. 青蛙跳台阶问题](https://leetcode-cn.com/problems/qing-wa-tiao-tai-jie-wen-ti-lcof/)

写一个函数，输入 n ，求斐波那契（Fibonacci）数列的第 n 项（即 F(N)）。斐波那契数列的定义如下：

```
F(0) = 0,   F(1) = 1
F(N) = F(N - 1) + F(N - 2), 其中 N > 1.
```

斐波那契数列由 0 和 1 开始，之后的斐波那契数就是由之前的两数相加而得出。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

**示例 1：**

```
输入：n = 2
输出：1
```

**示例 2：**

```
输入：n = 5
输出：5
```


**提示：**

* 0 <= n <= 100

## 动态规划

```java
class Solution {
    public int fib(int n) {
        // base case
        if (n == 0 || n == 1) return n;
        
        int pre = 0, cur = 1;
        for (int i = 2; i <= n; i++) {
            int sum = (pre + cur) % 1000000007;
            pre = cur;
            cur = sum;
        }
        return cur;
    }
}
```

## 带备忘录的递归

```java
class Solution {
    public int fib(int n) {
        // 备忘录初始为0
        int[] mem = new int[n + 1];
        return helper(mem, n);
    }
    
    private int helper(int[] mem, int n) {
        if (n == 0 || n == 1) return n;
        // 备忘录中有值，已经计算过，直接返回
        if (mem[n] != 0) return mem[n];
        mem[n] = helper(mem, n - 1) + helper(mem, n - 2);
        return mem[n];
    }
}
```

# [剑指 Offer 11. 旋转数组的最小数字](https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/)

## 同 [154. 寻找旋转排序数组中的最小值 II](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/)

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个递增排序的数组的一个旋转，输出旋转数组的最小元素。例如，数组 [3,4,5,1,2] 为 [1,2,3,4,5] 的一个旋转，该数组的最小值为1。  

**示例 1：**

```
输入：[3,4,5,1,2]
输出：1
```

**示例 2：**

```
输入：[2,2,2,0,1]
输出：0
```

## 二分查找

将中点元素 nums[mid] 与最右元素 nums[high] 比较：

* nums[mid] < nums[high] ，说明 nums[mid] 是最小值右侧的元素，最小值在左侧，下一次查找区间为左半部分。
* nums[mid] > nums[high] ，说明 nums[mid] 是最小值左侧的元素，最小值在右侧，下一次查找区间为右半部分。
* nums[mid] = nums[high] ，并不能确定 nums[mid] 究竟在最小值的左侧还是右侧。但无论 nums[high] 是不是最小值，都有一个它的替代品 nums[mid] ，因此可以忽略二分查找区间的右端点。

```java
class Solution {
    public int minArray(int[] numbers) {
        int low = 0;
        int high = numbers.length - 1;
        while (low < high) {
            int mid = low + (high - low) / 2;
            if (numbers[mid] > numbers[high]) {
                low = mid + 1;
            } else if (numbers[mid] < numbers[high]) {
                high = mid;
            } else {
                high -= 1;
            }
        }
        return numbers[low];
    }
}
```

* 时间复杂度：平均时间复杂度为 O(logn) ，如果数组是随机生成的，那么数组中包含相同元素的概率很低，在二分查找的过程中，大部分情况都会忽略一半的区间。而在最坏情况下，如果数组中的元素完全相同，那么 while 循环就需要执行 n 次，每次忽略区间的右端点，时间复杂度为 O(n) 。
* 空间复杂度：O(1)

# [剑指 Offer 12. 矩阵中的路径](https://leetcode-cn.com/problems/ju-zhen-zhong-de-lu-jing-lcof/)

## 同 [79. 单词搜索](https://leetcode-cn.com/problems/word-search/)

给定一个 m x n 二维字符网格 board 和一个字符串单词 word 。如果 word 存在于网格中，返回 true ；否则，返回 false 。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

 例如，在下面的 3×4 的矩阵中包含单词 "ABCCED"（单词中的字母已标出）。

![image-20210409142534030](G:\doc\Note\剑指Offer题解.assets\image-20210409142534030.png)

**示例 1：**

```
输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
输出：true
```

**示例 2：**

```
输入：board = [["a","b"],["c","d"]], word = "abcd"
输出：false
```

**提示：**

* 1 <= board.length <= 200
* 1 <= board[i].length <= 200
* board 和 word 仅由大小写英文字母组成

## 深度优先遍历 + 剪枝

**DFS：**

* 终止条件：
  * 返回 false ：行、列索引越界 **或** 当前矩阵元素与目标字符不同 **或** 当前矩阵元素已被访问过（访问过的元素替换为 '\0' ）。
  * 返回 true ：目标字符串 word 的索引指向最后一个字符，即 k == word.length - 1 时，字符串 word 已全部匹配。
* 递归步骤：
  * 标记当前矩阵元素： 将 `board[i][j]` 修改为 **空字符** `'\0'` ，代表此元素已访问过，防止之后搜索时重复访问。
  * 搜索下一单元格： 朝当前元素的 **上、下、左、右** 四个方向开启下层递归，只需找到一条可行路径就可以直接返回。
  * 还原当前矩阵元素： 将 `board[i][j]` 元素还原至初始值，即 `word[k]` 。

```java
class Solution {
    public boolean exist(char[][] board, String word) {
        char[] words = word.toCharArray();
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                if (dfs(board, words, i, j, 0)) return true;
            }
        }
        return false;
    }

    private boolean dfs(char[][] board, char[] words, int i, int j, int k) {
        if (i < 0 || i > board.length - 1 || j < 0 || j > board[0].length - 1 || board[i][j] != words[k]) return false;
        if (k == words.length - 1) return true;
        board[i][j] = '\0';
        boolean flag = dfs(board, words, i - 1, j, k + 1) || dfs(board, words, i + 1, j, k + 1) 
                        || dfs(board, words, i, j + 1, k + 1) || dfs(board, words, i, j - 1, k + 1);
        board[i][j] = words[k];
        return flag;
    }
}
```

设 M, N 分别为矩阵行列大小，K 为字符串 word 长度。

* 时间复杂度 O(3^K^MN) ：最差情况下，需要遍历矩阵中长度为 K 字符串的所有方案，时间复杂度为 O(3^K^)；矩阵中共有 MN 个起点，时间复杂度为 O(MN) 。
  
  * 方案数计算： 设字符串长度为 K ，搜索中每个字符有上、下、左、右四个方向可以选择，舍弃回头（上个字符）的方向，剩下 3 种选择，因此方案数的复杂度为 O(3^K^) 。
* 空间复杂度 O(K) ：搜索过程中的递归深度不超过 K ，因此系统因函数调用累计使用的栈空间占用 O(K)（因为函数返回后，系统调用的栈空间会释放）。最坏情况下 K = MN ，递归深度为 MN ，此时系统栈使用 O(MN) 的额外空间。

# [剑指 Offer 13. 机器人的运动范围](https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/)

地上有一个m行n列的方格，从坐标 [0,0] 到坐标 [m-1,n-1] 。一个机器人从坐标 [0, 0] 的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于k的格子。例如，当k为18时，机器人能够进入方格 [35, 37] ，因为3+5+3+7=18。但它不能进入方格 [35, 38]，因为3+5+3+8=19。请问该机器人能够到达多少个格子？

**示例 1：**

```
输入：m = 2, n = 3, k = 1
输出：3
```

**示例 2：**

```
输入：m = 3, n = 1, k = 0
输出：1
```

**提示：**

* 1 <= n,m <= 100
* 0 <= k <= 20

## 深度优先遍历

思路与 **剑指Offer 12** 类似。

```java
class Solution {

    int m, n;
    boolean[][] visited;    // 记录当前方格是否已访问 true：已访问

    public int movingCount(int m, int n, int k) {
        this.m = m;
        this.n = n;
        visited = new boolean[m][n];
        // 从 0,0 开始访问
        return dfs(0, 0, k);
    }

    // 深度优先遍历
    private int dfs(int i, int j, int k) {
        // 索引越界 或 当前方格已访问 或 行坐标和列坐标的数位之和大于k，直接返回
        if (i > m - 1 || j > n - 1 || visited[i][j] || digitSum(i, j) > k) return 0;
        
        visited[i][j] = true;
        
        // 沿着当前方格的右边和下边继续访问（从 0,0 开始访问，所以上边和左边已经访问过）
        return 1 + dfs(i + 1, j, k) + dfs(i, j + 1, k);
    }

    // 计算行坐标和列坐标的数位之和
    private int digitSum (int i, int j) {
        int sum = 0;
        while (i != 0) {
            sum += i % 10;
            i = i / 10;
        }
        while (j != 0) {
            sum += j % 10;
            j = j / 10;
        }
        return sum;
    }
}
```

* 时间复杂度：O(mn) ，最差情况下，机器人遍历矩阵所有单元格，此时时间复杂度为 O(mn) 。
* 空间复杂度：O(mn) ，使用辅助矩阵 visited 记录是否已访问。

## 广度优先遍历

```java
class Solution {
    public int movingCount(int m, int n, int k) {
        boolean[][] visited = new boolean[m][n];	// 记录当前方格是否已访问 true：已访问
        // 向右和向下的方向数组
        int[] dx = {0, 1};
        int[] dy = {1, 0};
        
        Queue<int[]> queue = new LinkedList<int[]>();
        queue.offer(new int[]{0, 0});		// 将坐标 0,0 入队
        visited[0][0] = true;		// 坐标 0,0 已访问
        int res = 1;
        while (!queue.isEmpty()) {
            int[] coordinate = queue.poll();	// 出队
            int x = coordinate[0], y = coordinate[1];
            for (int i = 0; i < 2; i++) {
                int newX = x + dx[i];
                int newY = y + dy[i];
                if (newX > m - 1 || newY > n - 1 || visited[newX][newY] || digitSum(newX, newY) > k) {
                    continue;	// 索引越界 或 当前方格已访问 或 行坐标和列坐标的数位之和大于k，跳过
                }
                queue.offer(new int[]{newX, newY});
                visited[newX][newY] = true;
                res++;
            }
        }        
        return res;
    }
    
    // 计算行坐标和列坐标的数位之和
    private int digitSum (int i, int j) {
        int sum = 0;
        while (i != 0) {
            sum += i % 10;
            i = i / 10;
        }
        while (j != 0) {
            sum += j % 10;
            j = j / 10;
        }
        return sum;
    }
}
```

# [剑指 Offer 15. 二进制中1的个数](https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/)

## 同 [191. 位1的个数](https://leetcode-cn.com/problems/number-of-1-bits/)

编写一个函数，输入是一个无符号整数（以二进制串的形式），返回其二进制表达式中数字位数为 '1' 的个数（也被称为汉明重量）。

**提示：**

* 请注意，在某些语言（如 Java）中，没有无符号整数类型。在这种情况下，输入和输出都将被指定为有符号整数类型，并且不应影响您的实现，因为无论整数是有符号的还是无符号的，其内部的二进制表示形式都是相同的。
* 在 Java 中，编译器使用二进制补码记法来表示有符号整数。因此，在 示例 3 中，输入表示有符号整数 -3。

**示例 1：**

```
输入：00000000000000000000000000001011
输出：3
解释：输入的二进制串 00000000000000000000000000001011 中，共有三位为 '1'。
```

**示例 2：**

```
输入：00000000000000000000000010000000
输出：1
解释：输入的二进制串 00000000000000000000000010000000 中，共有一位为 '1'。
```

**示例 3：**

```
输入：11111111111111111111111111111101
输出：31
解释：输入的二进制串 11111111111111111111111111111101 中，共有 31 位为 '1'。
```

**提示：**

* 输入必须是长度为 32 的 二进制串 。

**进阶：**

* 如果多次调用这个函数，你将如何优化你的算法？

## 循环检查二进制位

```java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int result = 0;
        for (int i = 0; i < 32; i++) {
            if ((n & (1 << i)) != 0) {
                result++;
            }
        }
        return result;
    }
}
```

* 时间复杂度：O(k)，k为二进制位数，即 k = 32。
* 空间复杂度：O(1)

## 位运算优化

n & (n−1)，其运算结果恰为把 n 的二进制位中的最低位的 1 变为 0 之后的结果。

不断让当前的 n 与 n - 1 做与运算，直到 n 变为 0 即可。

运算次数等于 n 的二进制位中 1 的个数。

```java
public class Solution {
    public int hammingWeight(int n) {
        int result = 0;
        while (n != 0) {
            n &= n - 1;
            result++;
        }
        return result;
    }
}
```

* 时间复杂度：O(logn)，循环次数等于 n 的二进制位中 1 的个数，最坏情况下 n 的二进制位全部为 1。需要循环 logn 次。
* 空间复杂度：O(1)

# [剑指 Offer 16. 数值的整数次方](https://leetcode-cn.com/problems/shu-zhi-de-zheng-shu-ci-fang-lcof/)

实现 pow(x, n) ，即计算 x 的 n 次幂函数（即，xn）。不得使用库函数，同时不需要考虑大数问题。

**示例 1：**

```
输入：x = 2.00000, n = 10
输出：1024.00000
```

**示例 2：**

```
输入：x = 2.10000, n = 3
输出：9.26100
```

**示例 3：**

```java
输入：x = 2.00000, n = -2
输出：0.25000
解释：2^-2 = 1/2^2 = 1/4 = 0.25
```

**提示：**

* -100.0 < x < 100.0
* -231 <= n <= 231-1
* -104 <= xn <= 104

## 快速幂

### 递归

快速幂的本质是分治算法。

如果要计算 x^64^ , 可以按照：x -> x^2^ -> x^4^  -> x^8^ -> x^16^  -> x^32^ -> x^64^ 的顺序，从 x 开始，每次直接把上一次的结果进行平方，计算 6 次就可以得到结果，而不需要对 x 乘 63 次 x 。

步骤：

* 当要计算 x^n^ 时，先递归地计算出 y = x^n/2^ 。
* 根据递归计算的结果，如果 n 为偶数，那么 x^n^ = y^2^ ；如果 n 为奇数，那么 x^n^ = y^2^ * x 。
* 递归的边界为 n = 0 ，任意数的 0 次方均为 1 。

```java
class Solution {
    public double myPow(double x, int n) {
        if (x == 0) return 0;
        long N = n;     // 将int转为long，防止溢出
        return N >= 0 ? quickMul(x, N) : 1.0 / quickMul(x, -N);
    }

    private double quickMul(double x, long N) {
        if (N == 0) {
            return 1.0;
        }
        if (N == 1) {
            return x;
        }
        double y = quickMul(x, N >> 1);
        return (N & 1) == 0 ? y * y : y * y * x;	// (N & 1) == 0 时 N 为偶数，1时为奇数
    }
}
```

* 时间复杂度：O(logn) ，递归的层数。
* 空间复杂度：O(logn) ，递归调用的栈空间。

### 迭代

```java
class Solution {
    public double myPow(double x, int n) {
        if (x == 0) return 0;
        long N = n;
        double res = 1.0;
        if (N < 0) {
            x = 1 / x;
            N = -N;
        }
        while (N > 0) {
            if ((N & 1) == 1) res *= x;
            x *= x;
            N >>= 1;
        }
        return res;
    }
}
```

* 时间复杂度：O(logn) ，二分的时间复杂度为对数级别。
* 空间复杂度：O(1) 

# [剑指 Offer 17. 打印从1到最大的n位数](https://leetcode-cn.com/problems/da-yin-cong-1dao-zui-da-de-nwei-shu-lcof/)

输入数字 n，按顺序打印出从 1 到最大的 n 位十进制数。比如输入 3，则打印出 1、2、3 一直到最大的 3 位数 999。

**示例 1:**

```
输入: n = 1
输出: [1,2,3,4,5,6,7,8,9]
```


说明：

* n 为正整数

## 大数全排列

此题应该考虑当 n 很大的时候产生的大数越界问题，因此大数的表示应该使用字符串 String 类型。

我们把数字的每一位都从 0 到 9 排列一遍，就得到了所有的十进制数。n 位所有十进制数其实就是 n 个从 0 到 9 的全排列。

全排列用递归很容易表达，数字的每一位都可能是 0-9 中的一个数，然后设置下一位。递归结束的条件是我们已经设置了数字的最后一位。

```java
class Solution {
    StringBuilder res;
    int count = 0;
    int nine = 0;
    int n, start;
    char[] num;
    
    public String printNumbers(int n) {
        this.n = n;
        res = new StringBuilder();
        num = new char[n];		// 长度为 n 的字符列表
        start = n - 1;
        recursive(0);		// 开始全排列递归
        res.deleteCharAt(res.length() - 1);		// 删除最后多余的逗号
        return res.toString();
    }
    
    void recursive(int x) {
        if(x == n) {		// 终止条件，已确定所有位
            String s = String.valueOf(num).substring(start);
            if(!s.equals("0")) res.append(s + ",");		// 要求从1开始打印，如果为0则舍弃
            if(n - start == nine) start--;		// 当前所有位为9，即将进位
            return;
        }
        for(int i = 0; i < 10; i++) {	// 遍历 0-9
            if(i == 9) nine++;
            num[x] = i + '0';		// 固定第 x 位为 i
            dfs(x + 1);				// 固定下一位
        }
        nine--;
    }
}
```

* 时间复杂度：O(10^n^) ，递归生成的排列的数量为 10^n^ 。
* 空间复杂度：O(10^n^) ，res 的长度为 10^n^ - 1 。

# [剑指 Offer 18. 删除链表的节点](https://leetcode-cn.com/problems/shan-chu-lian-biao-de-jie-dian-lcof/)

给定单向链表的头指针和一个要删除的节点的值，定义一个函数删除该节点。

返回删除后的链表的头节点。

**注意：**此题对比原题有改动

**示例 1:**

```
输入: head = [4,5,1,9], val = 5
输出: [4,1,9]
解释: 给定你链表中值为 5 的第二个节点，那么在调用了你的函数之后，该链表应变为 4 -> 1 -> 9.
```

**示例 2:**

```
输入: head = [4,5,1,9], val = 1
输出: [4,5,9]
解释: 给定你链表中值为 1 的第三个节点，那么在调用了你的函数之后，该链表应变为 4 -> 5 -> 9.
```

**说明：**

* 题目保证链表中节点的值互不相同
* 若使用 C 或 C++ 语言，你不需要 free 或 delete 被删除的节点

```java
class Solution {
    public ListNode deleteNode(ListNode head, int val) {
        if (head == null) return null;
        if (head.val == val) return head.next;
        ListNode pre = head, cur = head.next;
        while (cur != null && cur.val != val) {
            pre = cur;
            cur = cur.next;
        }
        if (cur != null) pre.next = cur.next;
        return head;
    }
}
```

* 时间复杂度：O(n)
* 空间复杂度：O(1)

# 剑指 Offer 18-II. & [LC 82. 删除排序链表中的重复元素 II](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

存在一个按升序排列的链表，给你这个链表的头节点 head ，请你删除链表中所有存在数字重复情况的节点，只保留原始链表中 没有重复出现 的数字。

返回同样按升序排列的结果链表。

**示例 1：**

```
输入：head = [1,2,3,3,4,4,5]
输出：[1,2,5]
```

**示例 2：**

```
输入：head = [1,1,1,2,3]
输出：[2,3]
```

## 一次遍历

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) {
            return head;
        }
        ListNode dummyNode = new ListNode(0, head);    // head可能会被删除，创建一个dummy指向head
        ListNode cur = dummyNode;
        while (cur.next != null && cur.next.next != null) {
            if (cur.next.val == cur.next.next.val) {
                int x = cur.next.val;          // 记录相等的值
                while (cur.next != null && cur.next.val == x) {     // 删除值相等的节点
                    cur.next = cur.next.next;
                }
            } else {
                cur = cur.next;
            }
        }
        return dummyNode.next;
    }
}
```

* 时间复杂度：O(n)
* 空间复杂度：O(1)

# [剑指 Offer 21. 调整数组顺序使奇数位于偶数前面](https://leetcode-cn.com/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof/)

输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有奇数位于数组的前半部分，所有偶数位于数组的后半部分。

**示例：**

```
输入：nums = [1,2,3,4]
输出：[1,3,2,4] 
注：[3,1,2,4] 也是正确的答案之一。
```

**提示：**

1. `0 <= nums.length <= 50000`
2. `1 <= nums[i] <= 10000`

## 首尾指针

```java
class Solution {
    public int[] exchange(int[] nums) {
        if (nums == null) return null;
        int left = 0, right = nums.length - 1;
        while (left < right) {
            while (left < right && (nums[left] & 1) == 1) left++;
            while (left < right && (nums[right] & 1) == 0) right--;               
            if (left < right) {   
                int temp = nums[left];
                nums[left] = nums[right];
                nums[right] = temp;
            }
        }
        return nums;
    }
}
```

* 时间复杂度：O(n)
* 空间复杂度：O(1)

# [剑指 Offer 22. 链表中倒数第k个节点](https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)

输入一个链表，输出该链表中倒数第 k 个节点。为了符合大多数人的习惯，本题从 1 开始计数，即链表的尾节点是倒数第 1 个节点。

例如，一个链表有 6 个节点，从头节点开始，它们的值依次是 1、2、3、4、5、6。这个链表的倒数第 3 个节点是值为 4 的节点。

**示例：**

```
给定一个链表: 1->2->3->4->5, 和 k = 2.

返回链表 4->5.
```

## 双指针

* p 和 q 指针指向 head，先让 q 指针向前走 k 步。
* p 和 q 同时向前走，q 走到终点时，p指向的即为倒数第 k 个节点。

```java
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        if (k == 0 || head == null) return null;
        ListNode p = head, q = head;
        while (k > 0) {
            q = q.next;
            k--;
        }
        while (q != null) {
            q = q.next;
            p = p.next;
        }
        return p;
    }
}
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
```

# [剑指 Offer 24. 反转链表](https://leetcode-cn.com/problems/fan-zhuan-lian-biao-lcof/)

## 同 [206. 反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)

定义一个函数，输入一个链表的头节点，反转该链表并输出反转后链表的头节点。

**示例:**

```
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
```

**限制：**

* 0 <= 节点个数 <= 5000

## 迭代

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode cur = head;
        ListNode pre = null;
        while (cur != null) {
            ListNode temp = cur.next;
            cur.next = pre;
            pre = cur;
            cur = temp;
        }
        return pre;
    }
}
```

## 递归

假设链表为：n~1~ -> n~2~ -> ... -> n~k-1~ -> n~k~ -> n~k+1~ -> ... -> n~m~ -> null

若从节点 n~k+1~ 到 n~m~ 已经被反转，而我们正处于 n~k~ ：n~1~ -> n~2~ -> ... -> n~k-1~ -> n~k~ -> **n~k+1~ <- ... <- n~m~**

n~k+1~ 的下一个节点应该指向 n~k~ ，则 n~k~.next.next = n~k~ 。n~1~ 的下一个节点必须指向 null 。

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode res = reverseList(head.next);
        head.next.next = head;
        head.next = null;
        return res;
    }
}
```

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        return recur(head, null);    // 调用递归并返回
    }
    private ListNode recur(ListNode cur, ListNode pre) {
        if (cur == null) return pre; // 终止条件
        ListNode res = recur(cur.next, cur);  // 递归后继节点
        cur.next = pre;              // 修改节点引用指向
        return res;                  // 返回反转链表的头节点
    }
}
```

* 时间复杂度：O(n) ，需要对链表的每个节点进行反转操作。
* 空间复杂度：O(n) ，空间复杂度主要取决于递归调用的栈空间，最多为 n 层。

# [剑指 Offer 25. 合并两个排序的链表](https://leetcode-cn.com/problems/he-bing-liang-ge-pai-xu-de-lian-biao-lcof/)

## 同 [21. 合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

输入两个递增排序的链表，合并这两个链表并使新链表中的节点仍然是递增排序的。

**示例1：**

```
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4
```

**限制：**

* 0 <= 链表长度 <= 1000

## 迭代

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode dummyNode = new ListNode(0);
        ListNode cur = dummyNode;
        while (l1 != null && l2 != null) {
            if (l1.val <= l2.val) {
                cur.next = l1;
                l1 = l1.next;
            } else {
                cur.next = l2;
                l2 = l2.next;
            }
            cur = cur.next;
        }
        cur.next = l1 == null ? l2 : l1;
        return dummyNode.next;
    }
}
```

* 时间复杂度：O(n + m) ，其中 n 和 m 分别为两个链表的长度。因为每次循环迭代中，l1 和 l2 只有一个元素会被放进合并链表中， 因此 while 循环的次数不会超过两个链表的长度之和。

* 空间复杂度：O(1)

## 递归

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
		if (l1 == null) {
            return l2;
        } else if (l2 == null) {
            return l1;
        } else if (l1.val < l2.val) {
            l1.next = mergeTwoLists(l1.next, l2);
            return l1;
        } else {
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }
    }
}
```

* 时间复杂度：O(n + m) 

* 空间复杂度：O(n + m)

# [剑指 Offer 26. 树的子结构](https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof/)

输入两棵二叉树A和B，判断B是不是A的子结构。(约定空树不是任意一个树的子结构)

B是A的子结构， 即 A中有出现和B相同的结构和节点值。

例如:
给定的树 A:

```
   3
  / \
  4  5
 / \
 1  2
```

给定的树 B：

```
   4 
  /
 1
```

返回 true，因为 B 与 A 的一个子树拥有相同的结构和节点值。

**示例 1：**

```
输入：A = [1,2,3], B = [3,1]
输出：false
```

**示例 2：**

```
输入：A = [3,4,5,1,2], B = [4,1]
输出：true
```

**限制：**

* 0 <= 节点个数 <= 10000

## 先序遍历

判断树 B 是否是树 A 的子结构，需完成以下两步工作：

* 先序遍历树 A 中的每个节点 n~A~ ；（对应函数 `isSubStructure(A, B)`）
* 判断树 A 中 **以 n~A~ 为根节点的子树** 是否包含树 B 。（对应函数 `recur(A, B)`）

**`recur(A, B)` 函数：**

* 终止条件：
  * 当节点 B 为空：说明树 B 已匹配完成，因此返回 true ；
  * 当节点 A 为空：说明已经越过树 A 叶子节点，即匹配失败，返回 false ；
  * 当节点 A 和 B 的值不同：说明匹配失败，返回 false ；
* 返回值：
  * 判断 A 和 B 的**左**子节点是否相等，即 `recur(A.left, B.left)` ；
  * 判断 A 和 B 的**右**子节点是否相等，即 `recur(A.right, B.right)` ；

**`isSubStructure(A, B)` 函数：**

* 当树 A 为空 **或** 树 B 为空时，直接返回 false ；
* 若树 B 是树 A 的子结构，则必满足以下三种情况之一，因此用或 `||` 连接；
  * 以 **节点 A 为根节点的子树** 包含树 B ，对应 `recur(A, B)`；
  * 树 B 是 **树 A 左子树** 的子结构，对应 `isSubStructure(A.left, B)`；
  * 树 B 是 **树 A 右子树** 的子结构，对应 `isSubStructure(A.right, B)`；

```java
class Solution {
    public boolean isSubStructure(TreeNode A, TreeNode B) {
        // 先序遍历
        return (A != null && B != null) && (recur(A, B) || isSubStructure(A.left, B) || isSubStructure(A.right, B));
    }

    private boolean recur(TreeNode A, TreeNode B) {
        if (B == null) return true;
        if (A == null || A.val != B.val) return false;
        return recur(A.left, B.left) && recur(A.right, B.right);
    }
}
```

* 时间复杂度 O(mn) ： 其中 m,n 分别为树 A 和 树 B 的节点数量；先序遍历树 A 占用 O(m) ，每次调用 recur(A, B) 判断占用 O(n) 。
* 空间复杂度 O(m) ： 当树 A 和树 B 都退化为链表时，递归调用深度最大。当 m <= n 时，遍历树 A 与递归判断的总递归深度为 m ；当 m > n 时，最差情况为遍历至树 A 叶子节点，此时总递归深度为 m 。

# [剑指 Offer 29. 顺时针打印矩阵](https://leetcode-cn.com/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/)

## 同 [54. 螺旋矩阵](https://leetcode-cn.com/problems/spiral-matrix/)

输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字。

**示例 1：**

```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[1,2,3,6,9,8,7,4,5]
```

**示例 2：**

```
输入：matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
输出：[1,2,3,4,8,12,11,10,9,5,6,7]
```

**限制：**

- `0 <= matrix.length <= 100`
- `0 <= matrix[i].length <= 100`

## 模拟

顺时针打印矩阵的顺序是 **“从左向右、从上向下、从右向左、从下向上”** 循环。

设定上、下、左、右四个边界，由外层向内层收缩。

```java
class Solution {
    public int[] spiralOrder(int[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) return new int[0];
        int rows = matrix.length, cols = matrix[0].length;
        int l = 0, r = cols - 1;     // 左边界和右边界
        int t = 0, b = rows - 1;     // 上边界和下边界
        int[] res = new int[rows * cols];
        int x = 0;
        while (true) {
            for (int i = l; i <= r; i++) res[x++] = matrix[t][i];   // 从左往右
            if (++t > b) break;     // 上边界向下收缩，++t
            for (int i = t; i <= b; i++) res[x++] = matrix[i][r];   // 从上往下
            if (--r < l) break;     // 右边界向左收缩，--r
            for (int i = r; i >= l; i--) res[x++] = matrix[b][i];   // 从右往左
            if (--b < t) break;     // 下边界向上收缩，--b
            for (int i = b; i >= t; i--) res[x++] = matrix[i][l];   // 从下往上
            if (++l > r) break;     // 左边界向右收缩，++l
        }
        return res;
    }
}
```

* 时间复杂度：O(mn) ，m, n 分别为矩阵行数和列数。
* 空间复杂度：O(1)



# [剑指 Offer 31. 栈的压入、弹出序列](https://leetcode-cn.com/problems/zhan-de-ya-ru-dan-chu-xu-lie-lcof/)

输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如，序列 {1,2,3,4,5} 是某栈的压栈序列，序列 {4,5,3,2,1} 是该压栈序列对应的一个弹出序列，但 {4,3,5,1,2} 就不可能是该压栈序列的弹出序列。

**示例 1：**

```
输入：pushed = [1,2,3,4,5], popped = [4,5,3,2,1]
输出：true
解释：我们可以按以下顺序执行：
push(1), push(2), push(3), push(4), pop() -> 4,
push(5), pop() -> 5, pop() -> 3, pop() -> 2, pop() -> 1
```

**示例 2：**

```
输入：pushed = [1,2,3,4,5], popped = [4,3,5,1,2]
输出：false
解释：1 不能在 2 之前弹出。
```

**提示：**

* 0 <= pushed.length == popped.length <= 1000
* 0 <= pushed[i], popped[i] < 1000
* pushed 是 popped 的排列。

## 辅助栈

用一个辅助栈模拟压入、弹出操作的排列。

* 按照压栈序列的顺序进行入栈。
* 若 栈顶元素 = 弹出序列的当前元素，说明栈顶元素符合弹出序列的顺序，将栈顶元素弹出，直到 栈顶元素 != 弹出序列的当前元素。
* 重复执行上述操作，直到压栈序列的所有元素都已入过辅助栈。若辅助栈为空，说明该弹出序列符合要求，返回 true 。

```java
class Solution {
    public boolean validateStackSequences(int[] pushed, int[] popped) {
        Deque<Integer> stack = new LinkedList<>();
        int i = 0;
        for (int num : pushed) {
            stack.push(num);
            while (!stack.isEmpty() && stack.peek() == popped[i]) {
                stack.pop();
                i++;
            }
        }
        return stack.isEmpty();
    }
}
```

* 时间复杂度：O(n) ，每个元素最多入栈与出栈一次。
* 空间复杂度：O(n) ，辅助栈最多同时存储 n 个元素。

# [剑指 Offer 32 - I. 从上到下打印二叉树](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-lcof/)

从上到下打印出二叉树的每个节点，同一层的节点按照从左到右的顺序打印。

例如:
给定二叉树: `[3,9,20,null,null,15,7]`,

```
    3
   / \
  9  20
    /  \
   15   7
```

返回：

```
[3,9,20,15,7]
```

**提示：**

* `节点总数 <= 1000`

## BFS

BFS 通常借助 **队列** 的先入先出特性来实现。

```
class Solution {
    public int[] levelOrder(TreeNode root) {
        if (root == null) return new int[0];
        Queue<TreeNode> queue = new LinkedList<>();
        List<Integer> list = new ArrayList<>();
        queue.offer(root);
        while (!queue.isEmpty()) {
            TreeNode temp = queue.poll();
            list.add(temp.val);
            if (temp.left != null) queue.offer(temp.left);
            if (temp.right != null) queue.offer(temp.right);
        }
        int[] res = new int[list.size()];
        for (int i = 0; i < list.size(); i++) {
            res[i] = list.get(i);
        }
        return res;
    }
}
```

* 时间复杂度：O(n)
* 空间复杂度：O(n) ，最差情况下，即当树为平衡二叉树时，最多有 N/2 个树节点同时在队列中，使用 O(N) 大小的额外空间。

# [剑指 Offer 32 - II. 从上到下打印二叉树 II](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/)

## 同 [102. 二叉树的层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

从上到下按层打印二叉树，同一层的节点按从左到右的顺序打印，每一层打印到一行。

例如:
给定二叉树: `[3,9,20,null,null,15,7]`,

```
    3
   / \
  9  20
    /  \
   15   7
```

返回其层次遍历结果：

```
[
  [3],
  [9,20],
  [15,7]
]
```

**提示：**

* `节点总数 <= 1000`

## BFS

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        Queue<TreeNode> queue = new LinkedList<>();
        List<List<Integer>> res = new ArrayList<>();
        if (root != null) queue.offer(root);
        while (!queue.isEmpty()) {
            List<Integer> tempList = new ArrayList<>();
            for (int i = queue.size(); i > 0; i--) {	// *****分层输出***** 
                TreeNode node = queue.poll();
                tempList.add(node.val);
                if (node.left != null) queue.offer(node.left);
                if (node.right != null) queue.offer(node.right);
            }
            res.add(tempList);
        }
        return res;
    }
}
```

# [剑指 Offer 32 - III. 从上到下打印二叉树 III](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof/)

请实现一个函数按照之字形顺序打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右到左的顺序打印，第三行再按照从左到右的顺序打印，其他行以此类推。

例如:
给定二叉树: `[3,9,20,null,null,15,7]`,

```
    3
   / \
  9  20
    /  \
   15   7
```

返回其层次遍历结果：

```
[
  [3],
  [20,9],
  [15,7]
]
```

**提示：**

* `节点总数 <= 1000`

## 层序遍历 + 双端队列

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        Queue<TreeNode> queue = new LinkedList<>();
        List<List<Integer>> res = new ArrayList<>();
        if(root != null) queue.add(root);
        while(!queue.isEmpty()) {
            LinkedList<Integer> tmp = new LinkedList<>();	// 双端队列
            for(int i = queue.size(); i > 0; i--) {
                TreeNode node = queue.poll();
                if(res.size() % 2 == 0) tmp.addLast(node.val);
                else tmp.addFirst(node.val);
                if(node.left != null) queue.add(node.left);
                if(node.right != null) queue.add(node.right);
            }
            res.add(tmp);
        }
        return res;
    }
}

```

## 层序遍历 + 双端队列（奇偶层逻辑分离）

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        Deque<TreeNode> deque = new LinkedList<>();
        List<List<Integer>> res = new ArrayList<>();
        if(root != null) deque.add(root);
        while(!deque.isEmpty()) {
            // 打印奇数层
            List<Integer> tmp = new ArrayList<>();
            for(int i = deque.size(); i > 0; i--) {
                // 从左向右打印
                TreeNode node = deque.removeFirst();
                tmp.add(node.val);
                // 先左后右加入下层节点
                if(node.left != null) deque.addLast(node.left);
                if(node.right != null) deque.addLast(node.right);
            }
            res.add(tmp);
            if(deque.isEmpty()) break; // 若为空则提前跳出
            // 打印偶数层
            tmp = new ArrayList<>();
            for(int i = deque.size(); i > 0; i--) {
                // 从右向左打印
                TreeNode node = deque.removeLast();
                tmp.add(node.val);
                // 先右后左加入下层节点
                if(node.right != null) deque.addFirst(node.right);
                if(node.left != null) deque.addFirst(node.left);
            }
            res.add(tmp);
        }
        return res;
    }
}
```

## 双栈

使用两个栈交替存储每一层的节点。

位于奇数层时，先存储左子树节点，后存储右子树节点。

位于偶数层时，先存储右子树节点，后存储左子树节点。

两个栈均为空时，遍历结束。

# [剑指 Offer 33. 二叉搜索树的后序遍历序列](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/)

输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历结果。如果是则返回 `true`，否则返回 `false`。假设输入的数组的任意两个数字都互不相同。

参考以下这颗二叉搜索树：

```
     5
    / \
   2   6
  / \
 1   3
```

**示例 1：**

```
输入: [1,6,3,2,5]
输出: false
```

**示例 2：**

```
输入: [1,3,2,6,5]
输出: true
```

**提示：**

* `数组长度 <= 1000`

## 递归分治

**二叉搜索树：** 左子树中所有节点的值 < 根节点的值；右子树中所有节点的值 > 根节点的值；其左、右子树也分别为二叉搜索树。

**后序遍历：**遍历顺序为 [ 左子树、右子树、根节点 ] 。

通过递归判断所有子树的后序遍历是否满足二叉搜索树的定义，若所有子树都正确，则此序列为二叉搜索树的后序遍历。

二叉搜索树的后序遍历序列中，前边的部分为左子树序列，所有节点的值均小于根节点；后边的部分为右子树序列，所有节点的值均大于根节点；最后一个元素为根节点。

```java
class Solution {
    public boolean verifyPostorder(int[] postorder) {
        return recur(postorder, 0, postorder.length - 1);
    }

    private boolean recur(int[] postorder, int i, int j) {
        // 后序遍历顺序为 [左、右、根] ，所以 j 指向的元素为根节点
        if (i >= j) return true;	// 说明树节点数量小于等于1，直接返回true
        int p = i;
        while (postorder[p] < postorder[j]) p++;	// 循环结束后，p指向的为右子树后序遍历序列中的每一个节点
        int m = p;
        while (postorder[p] > postorder[j]) p++;	// 循环结束后，p指向的为根节点，即j
        // 若p==j，即此时p指向根节点，说明该子树符合二叉搜索树的要求，继续判断其左右子树是否满足要求；若p!=j，则该子树不是二叉搜索树
        return p == j && recur(postorder, i, m - 1) && recur(postorder, m, j - 1);
    }
}
```

* 时间复杂度：O(n^2^) ， 每次调用 recur(i, j) 减去一个根节点，因此递归占用 O(n) ；最差情况下（即当树退化为链表），每轮递归都需遍历树所有节点，占用 O(n) 。
* 空间复杂度：O(n) ，最差情况下（即当树退化为链表），递归深度将达到 n 。

# [剑指 Offer 39. 数组中出现次数超过一半的数字](https://leetcode-cn.com/problems/shu-zu-zhong-chu-xian-ci-shu-chao-guo-yi-ban-de-shu-zi-lcof/)

## 同 [169. 多数元素](https://leetcode-cn.com/problems/majority-element/)

给定一个大小为 n 的数组，找到其中的多数元素。多数元素是指在数组中出现次数 大于 ⌊ n/2 ⌋ 的元素。

你可以假设数组是非空的，并且给定的数组总是存在多数元素。

**示例 1：**

```
输入：[3,2,3]
输出：3
```

**示例 2：**

```
输入：[2,2,1,1,1,2,2]
输出：2
```

**进阶：**

尝试设计时间复杂度为 O(n)、空间复杂度为 O(1) 的算法解决此问题。

## 排序

先对数组进行排序，因为多数元素的出现次数超过 n/2 ，所以排序后的数组的第 n/2 元素即为要找的元素。

## 哈希表

key 为数组中出现的数字， value 为数字出现的次数。

遍历哈希表，找出 value 值最大的键值对。

```java
class Solution {
    public int majorityElement(int[] nums) {
        Map<Integer, Integer> map = new HashMap<Integer, Integer>();
        Map.Entry<Integer, Integer> majorityEntry = null;
        for (int num : nums) {
            if (map.containsKey(num)) {
                map.put(num, map.get(num) + 1);
            } else {
                map.put(num, 1);
            }
        }

        for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
            if (majorityEntry == null || entry.getValue() > majorityEntry.getValue()) {
                majorityEntry = entry;
            }
        }

        return majorityEntry.getKey();
    }
}
```

* 时间复杂度：O(n)
* 空间复杂度：O(n)

## Boyer-Moore （摩尔）投票算法

若记 **众数** 的票数为 +1 ，**非众数** 的票数为 -1 ，则一定有所有数字的 **票数和 >0** 。

若数组的前 a 个数字的 **票数和 =0**，则数组剩余 (n-a) 个数字的 **票数和一定仍** >0 ，即后 (n-a) 个数字的 **众数仍为** x 。

利用此特性，每轮假设第一个数为 **众数**，发生 **票数和 = 0** 都可以 **缩小剩余数组区间**。当遍历完成时，最后一轮假设的数字即为众数。

> 通俗理解：
>
> 如果候选人不是maj，则maj会和其他非候选人一起反对候选人，所以候选人一定会下台（票数和为 0 时发生换届选举）
>
> 如果候选人是maj，则maj会支持自己，其他人会反对，但因为maj票数超过一半，所以maj一定会成功当选
>
> 无论如何，最终剩下的都是maj。

```java
class Solution {
    public int majorityElement(int[] nums) {     
        int count = 0;
        Integer candidate = null;

        for (int num : nums) {
            if (count == 0) {
                candidate = num;
            }
            count += (num == candidate) ? 1 : -1;
        }

        return candidate;
    }
}
```

* 时间复杂度：O(n)
* 空间复杂度：O(1)

# [剑指 Offer 52. 两个链表的第一个公共节点](https://leetcode-cn.com/problems/liang-ge-lian-biao-de-di-yi-ge-gong-gong-jie-dian-lcof/)

## 同 [160. 相交链表](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)

输入两个链表，找出它们的第一个公共节点。

**示例 1：**

![image-20210417163617801](G:\doc\Note\剑指Offer题解.assets\image-20210417163617801.png)

```java
输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
输出：Reference of the node with value = 8
输入解释：相交节点的值为 8 （注意，如果两个列表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,0,1,8,4,5]。在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。
```

 **示例 2：**

![image-20210417163650573](G:\doc\Note\剑指Offer题解.assets\image-20210417163650573.png)

```
输入：intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
输出：null
输入解释：从各自的表头开始算起，链表 A 为 [2,6,4]，链表 B 为 [1,5]。由于这两个链表不相交，所以 intersectVal 必须为 0，而 skipA 和 skipB 可以是任意值。
解释：这两个链表不相交，因此返回 null。
```

**注意：**

* 如果两个链表没有交点，返回 null 。
* 在返回结果后，两个链表仍须保持原有的结构。
* 可假定整个链表结构中没有循环。
* 程序尽量满足 O(n) 时间复杂度，且仅用 O(1) 内存。

## 双指针

设**第一个公共节点**为 node ，链表 headA 的节点数量为 a ，链表 headB 的节点数量为 b ，两链表的公共尾部的节点数量为 c ，则有：

* 头节点 headA 到 node 前，共有 a - c 个节点。
* 头节点 headB 到 node 前，共有 b - c 个节点。

构建两个节点指针 `A` , `B` 分别指向两链表头节点 `headA` , `headB` ，做如下操作：

* 指针 `A` 先遍历完链表 `headA` ，再开始遍历链表 `headB` ，当走到 `node` 时，共走步数为：a + (b - c) 。
* 指针 `B` 先遍历完链表 `headB` ，再开始遍历链表 `headA` ，当走到 `node` 时，共走步数为：b + (a - c) 。

a + (b - c) = b + (a - c) ，此时 A，B 指针重合，并有两种情况：

* 或两个链表**有**公共尾部（c > 0）：A，B 同时指向**第一个公共节点** node 。
* 或两个链表**没有**公共尾部（c = 0）：A，B 同时指向 null 。

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode A = headA, B = headB;
        while (A != B) {
            A = A != null ? A.next : headB;
            B = B != null ? B.next : headA;
        }
        return A;
    }
}
```

* 时间复杂度：O(a+b) ，最差情况下（|a - b| = 1，c = 0），需遍历 a + b 个节点。
* 空间复杂度：O(1)

# [剑指 Offer 56 - I. 数组中数字出现的次数](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof/)

一个整型数组 `nums` 里除两个数字之外，其他数字都出现了两次。请写程序找出这两个只出现一次的数字。要求时间复杂度是O(n)，空间复杂度是O(1)。

**示例 1：**

```
输入：nums = [4,1,4,6]
输出：[1,6] 或 [6,1]
```

**示例 2：**

```
输入：nums = [1,2,10,4,1,4,3,3]
输出：[2,10] 或 [10,2]
```

**限制：**

- `2 <= nums.length <= 10000`

## 位运算

> 要求时间复杂度是O(n)，空间复杂度是O(1)。所以不能用暴力解法或借助哈希表等辅助空间。

当一个整型数组 `nums` 里除 **一个** 数字之外，其他数字都出现了两次时（nums = [a, a, b, b, ..., x]）：

对 nums 中所有的数字执行 **异或**（两个相同数字结果为0） 运算，则最终的结果即为只出现一次的数字 x 。

> 但本题为 nums 数组中有**两个**只出现一次的数字。

设两个只出现一次的数字为 x , y ，由于 x != y ，则 x 和 y 二进制至少有一位不同（即分别为 0 和 1 ），根据此位可以将 nums 拆分为分别包含 x 和 y 的两个子数组。

即将问题简化为上述的 “ 数组中只包含一个出现一次的数字 ”  问题。

**解题步骤：**

* 遍历 nums 执行异或运算
  * 设 nums = [a, a, b, b, ..., x, y] ，则异或结果为 x ⊕ y 。
* 寻找 x 和 y 首个不同的二进制位
  * 若 x ⊕ y 的某个二进制位为 1 ，说明 x 和 y 的此位一定不同（一个为 0， 一个为 1）。因此找到 x ⊕ y 某个为 1 的二进制位，即可区分出 x 和 y ，将 nums 数组拆分为分别包含 x 和 y 的两个数组。
  * 若 a & 0001 == 1 ，则 a 的第一位为 1 。若 a & 0010 == 1 ，则 a 的第二位为 1 。
  * 因此，设置 m = 1 ，通过与运算从右向左循环判断，可获取整数 x ⊕ y 的首位 1 。
* 利用 m 中记录的 1 的位置将数组拆分为两个子数组
* 分别对两个子数组进行异或运算，即可分别得到只出现一次的数字。

```java
class Solution {
    public int[] singleNumbers(int[] nums) {
        int x = 0, y = 0, z = 0, m = 1;     // 设 x, y 分别为两个出现一次的数
        for (int num : nums) {
            z ^= num;       // z的结果为 x ^ y 
        }
        while ((z & m) == 0) {      // 找到 z 中首次出现 1 的位
            m <<= 1;
        }
        for (int num : nums) {
            if ((num & m) == 0) x ^= num;
            else y ^= num;
        }
        return new int[] { x, y };
    }
}
```

# [剑指 Offer 59 - I. 滑动窗口的最大值](https://leetcode-cn.com/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof/)

## 同 [239. 滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/) 

给定一个数组 nums 和滑动窗口的大小 k，请找出所有滑动窗口里的最大值。

**示例:**

```
输入: nums = [1,3,-1,-3,5,3,6,7], 和 k = 3
输出: [3,3,5,5,6,7] 
解释: 
 滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

**提示：**

你可以假设 k 总是有效的，在输入数组不为空的情况下，1 ≤ k ≤ 输入数组的大小。

## 单调队列

用**双端队列**保存有可能是滑动窗口最大值的数字的下标。

在存入一个数字的下标之前，先判断队列里已有数字是否**小于**待存入的数字，若是，则这些数字**不可能**是滑动窗口**最大值**，将它们依次从队尾删除。

此时，**队头**元素为滑动窗口最大值。

如果队头元素已经从窗口滑出，则将滑出的数字从队头删除。

因为队头和队尾都有可能删除数字，所以需要使用双端队列。

因为队列中元素满足严格单调递减，所以这种单调性的队列一般称作**单调队列**。

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if(nums.length == 0 || k == 0) return new int[0];

        // 单调队列
        Deque<Integer> deque = new LinkedList<>();
        int[] res = new int[nums.length - k + 1];

        for (int i = 0; i < k; i++) {
            while (!deque.isEmpty() && nums[i] >= nums[deque.peekLast()]) {
                deque.pollLast();
            }
            deque.offerLast(i);
        }
        res[0] = nums[deque.peekFirst()];

        for (int i = k; i < nums.length; i++) {
            while (!deque.isEmpty() && nums[i] >= nums[deque.peekLast()]) {
                deque.pollLast();
            }
            if (!deque.isEmpty() && deque.peekFirst() <= i - k) {
                deque.pollFirst();          // 将窗口最左侧元素移出队列
            }
            deque.offerLast(i);         // 窗口向右移动一个位置，将新元素加入队列
            res[i - k + 1] = nums[deque.peekFirst()];
        }
        return res;
    }
}
```

* 时间复杂度：O(n) ，n 是数组 nums 的长度，每一个下标恰好被放入队列一次，并且最多被弹出队列一次。
* 空间复杂度：O(k) ，双端队列最多同时存储 k（窗口大小）个元素。

# [剑指 Offer 30. 包含min函数的栈](https://leetcode-cn.com/problems/bao-han-minhan-shu-de-zhan-lcof/)

## 同 [155. 最小栈](https://leetcode-cn.com/problems/min-stack/)

定义栈的数据结构，请在该类型中实现一个能够得到栈的最小元素的 min 函数在该栈中，调用 min、push 及 pop 的时间复杂度都是 O(1)。

**示例:**

```
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.min();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.min();   --> 返回 -2.
```

**提示：**

* 各函数的调用总次数不超过 20000 次

## 辅助栈

```java
class MinStack {

    Deque<Integer> stack;
    Deque<Integer> minStack;	// 存储最小值

    /** initialize your data structure here. */
    public MinStack() {
        stack = new LinkedList<Integer>();
        minStack = new LinkedList<Integer>();
    }
    
    public void push(int val) {
        stack.push(val);
        if (minStack.isEmpty() || val <= minStack.peek()) {
            minStack.push(val);
        }
    }
    
    public void pop() {
        if (stack.pop().equals(minStack.peek())) {  // 不能用 ==
            minStack.pop();
        }
    }
    
    public int top() {
        return stack.peek();
    }
    
    public int getMin() {
        return minStack.peek();
    }
}
```

