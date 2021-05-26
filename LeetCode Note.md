# 141. 环形链表

给定一个链表，判断链表中是否有环。

如果链表中有某个节点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。

## Floyd 判圈算法（龟兔赛跑算法）

假想「乌龟」和「兔子」在链表上移动，「兔子」跑得快，「乌龟」跑得慢。当「乌龟」和「兔子」从链表上的同一个节点开始移动时，如果该链表中没有环，那么「兔子」将一直处于「乌龟」的前方；如果该链表中有环，那么「兔子」会先于「乌龟」进入环，并且一直在环内移动。等到「乌龟」进入环时，由于「兔子」的速度快，它一定会在某个时刻与乌龟相遇，即套了「乌龟」若干圈。

我们可以根据上述思路来解决本题。具体地，我们定义两个指针，一快一满。慢指针每次只移动一步，而快指针每次移动两步。初始时，慢指针在位置 head，而快指针在位置 head.next。这样一来，如果在移动的过程中，快指针反过来追上慢指针，就说明该链表为环形链表。否则快指针将到达链表尾部，该链表不为环形链表。

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        if (head == null || head.next == null) {
            return false;
        }
        ListNode slow = head;
        ListNode fast = head.next;
        while (slow != fast) {
            if (fast == null || fast.next == null) {
                return false;
            }
            slow = slow.next;
            fast = fast.next.next;
        }
        return true;
    }
}

class ListNode {
	int val;
	ListNode next;
	ListNode(int x) {
		val = x;
		next = null;
	}
}
```

# [142. 环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 `null`。

## 快慢指针

我们使用两个指针，fast与slow。它们起始都位于链表的头部。随后，slow指针每次向后移动一个位置，而fast指针向后移动两个位置。如果链表中存在环，则fast指针最终将再次与slow指针在环中相遇。

设链表中环外部分的长度为a。slow指针进入环后，又走了b的距离与fast相遇。此时，fast指针已经走完了环的n圈，因此它走过的总距离为a + n(b + c) + b = a + (n + 1)b + nc。

根据题意，任意时刻，fast指针走过的距离都为slow指针的2倍。因此，我们有
a + (n + 1)b + nc = 2(a + b)  ⟹  a = c + (n − 1)(b + c)

有了a = c + (n − 1)(b + c)的等量关系，我们会发现：从相遇点到入环点的距离加上n-1圈的环长，恰好等于从链表头部到入环点的距离。

因此，当发现slow与fast相遇时，我们再额外使用一个指针ptr。起始，它指向链表头部；随后，它和slow每次向后移动一个位置。最终，它们会在入环点相遇。

<img src="https://i.loli.net/2020/10/11/HUfSod9RwEWP7yb.png" style="zoom: 50%;" />

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if (head == null) {
            return null;
        }
        ListNode slow = head, fast = head;
        while (fast != null) {
            slow = slow.next;
            if (fast.next != null) {
                fast = fast.next.next;
            } else {
                return null;
            }
            if (fast == slow) {
                ListNode ptr = head;
                while (ptr != slow) {
                    ptr = ptr.next;
                    slow = slow.next;
                }
                return ptr;
            }
        }
        return null;
    }
}
```

# [416. 分割等和子集](https://leetcode-cn.com/problems/partition-equal-subset-sum/)（不会）

给定一个**只包含正整数**的**非空**数组。是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

**注意:**

1. 每个数组中的元素不会超过 100

2. 数组的大小不会超过 200

**示例 1:**

```
输入: [1, 5, 11, 5]

输出: true

解释: 数组可以分割成 [1, 5, 5] 和 [11].
```

**示例 2:**

```
输入: [1, 2, 3, 5]

输出: false

解释: 数组不能分割成两个元素和相等的子集.
```

## 动态规划

类似于传统的「0-10−1 背包问题」，可以使用动态规划求解。

在使用动态规划求解之前，首先需要进行以下判断。

* 根据数组的长度`n`判断数组是否可以被划分。如果`n<2`，则不可能将数组分割成元素和相等的两个子集，因此直接返回`false`。

* 计算整个数组的元素和`sum`以及最大元素`maxNum`。如果`sum`是奇数，则不可能将数组分割成元素和相等的两个子集，因此直接返回`false`。如果`sum`是偶数，则令`target = sum / 2`，需要判断是否可以从数组中选出一些数字，使得这些数字的和等于`target`。如果`maxNum > target`，则除了`maxNum`以外的所有元素之和一定小于`target`，因此不可能将数组分割成元素和相等的两个子集，直接返回`false`。

```java
class Solution {
    public boolean canPartition(int[] nums) {
        int n = nums.length;
        if (n < 2) {
            return false;
        }
        int sum = 0, maxNum = 0;
        for (int num : nums) {
            sum += num;
            maxNum = Math.max(maxNum, num);
        }
        if (sum % 2 != 0) {
            return false;
        }
        int target = sum / 2;
        if (maxNum > target) {
            return false;
        }
        boolean[] dp = new boolean[target + 1];
        dp[0] = true;
        for (int i = 0; i < n; i++) {
            int num = nums[i];
            for (int j = target; j >= num; --j) {
                dp[j] |= dp[j - num];
            }
        }
        return dp[target];
    }
}
```

# [11. 盛最多水的容器](https://leetcode-cn.com/problems/container-with-most-water/)

给你 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0) 。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

说明：你不能倾斜容器。

```
输入：[1,8,6,2,5,4,8,3,7]
输出：49 
解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
```

## 双指针

使用左右指针分别指向数组的两端，计算面积。

面积大小受到高度值小的一方限制，故移动指向的高度值小的指针，计算新的面积。

直到两个指针重合，最大的面积值即为最终结果。

```java
class Solution {
    public int maxArea(int[] height) {
        int l = 0;
        int r = height.length - 1;
        int result = 0;

        while (l < r) {
            int area = Math.min(height[l], height[r]) * (r - l);
            result = Math.max(result, area);
            if (height[l] <= height[r]) {
                l++;
            } else {
                r--;
            }
        }
        return result;
    }
}
```

# [20. 有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。

有效字符串需满足：

左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。

示例 1：

```
输入：s = "()"
输出：true
```

示例 2：

```
输入：s = "()[]{}"
输出：true
```

示例 3：

```
输入：s = "(]"
输出：false
```

**提示：**

- `1 <= s.length <= 104`
- `s` 仅由括号 `'()[]{}'` 组成

## 栈

将左括号入栈，遇到右括号时与栈顶元素比较，若不对应则返回 false 。

最后若栈为空，则返回 true 。

注：符合条件的字符串长度一定为偶数，若为奇数则直接返回 false，不需要后续判断。

```java
class Solution {
    public boolean isValid(String s) {
        Deque<Character> stack = new LinkedList<Character>();
        if (s.length() % 2 == 1) {  // s的长度必须是偶数
            return false;
        }
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (c == '(') {
                stack.push(')');
            } else if (c == '[') {
                stack.push(']');      
            } else if (c == '{') {
                stack.push('}');           
            } else if (stack.isEmpty() || stack.pop() != c) {
                return false;          
            }
        }
        return stack.isEmpty();
    }
}
```

# [155. 最小栈](https://leetcode-cn.com/problems/min-stack/)

设计一个支持 push ，pop ，top 操作，并能在常数时间内检索到最小元素的栈。

* push(x) —— 将元素 x 推入栈中。
* pop() —— 删除栈顶的元素。
* top() —— 获取栈顶元素。
* getMin() —— 检索栈中的最小元素。

**示例:**

```
输入：
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]

输出：
[null,null,null,null,-3,null,0,-2]

解释：
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.getMin();   --> 返回 -2.
```

**提示：**

- `pop`、`top` 和 `getMin` 操作总是在 **非空栈** 上调用。

## 辅助栈

使用一个辅助栈保存最小值，并与元素栈同步插入与删除。

```java
class MinStack {
    Deque<Integer> xStack;
    Deque<Integer> minStack;

    public MinStack() {
        xStack = new LinkedList<Integer>();
        minStack = new LinkedList<Integer>();
        minStack.push(Integer.MAX_VALUE);
    }
    
    public void push(int x) {
        xStack.push(x);
        minStack.push(Math.min(minStack.peek(), x));
    }
    
    public void pop() {
        xStack.pop();
        minStack.pop();
    }
    
    public int top() {
        return xStack.peek();
    }
    
    public int getMin() {
        return minStack.peek();
    }
}
```

## 链表

链表节点同时保存元素值和当前最小值。

```java
class MinStack {
    private Node head;
    
    public void push(int x) {
        if (head == null) 
            head = new Node(x, x);
        else 
            head = new Node(x, Math.min(x, head.min), head);
    }

    public void pop() {
        head = head.next;
    }

    public int top() {
        return head.val;
    }

    public int getMin() {
        return head.min;
    }
    
    private class Node {
        int val;
        int min;
        Node next;
        
        private Node(int val, int min) {
            this(val, min, null);
        }
        
        private Node(int val, int min, Node next) {
            this.val = val;
            this.min = min;
            this.next = next;
        }
    }
}
```

# [70. 爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)

假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

**注意：**给定 n 是一个正整数。

**示例 1：**

```
输入： 2
输出： 2
解释： 有两种方法可以爬到楼顶。

1. 1 阶 + 1 阶
2. 2 阶
```

**示例 2：**

```
输入： 3
输出： 3
解释： 有三种方法可以爬到楼顶。

1. 1 阶 + 1 阶 + 1 阶
2. 1 阶 + 2 阶
3. 2 阶 + 1 阶
```

## 动态规划

状态转移方程：f(n) = f(n-1) + f(n-2)		*斐波那契数列*

```java
class Solution {
    public int climbStairs(int n) {
 		if (n == 1 || n == 2) return n;
        int[] dp = new dp[n+1];
        dp[1] = 1;
        dp[2] = 2;
        for (int i = 3; i < n + 1; i++) {
            dp[i] = dp[i-1] + dp[i-2];
        }
        return dp[n];
    }
}
```

* 时间复杂度：O(n)
* 空间复杂度：O(n)

```java
// 优化空间复杂度
class Solution {
    public int climbStairs(int n) {
        int p = 0;
        int q = 0;
        int result = 1;
        for (int i = 1; i <= n; i++) {
            p = q;
            q = result;
            result = p + q;
        }
        return result;
    }
}
```

* 时间复杂度：O(n)
* 空间复杂度：O(1)

## 思考1：加入限制条件，不能连续跳两个台阶



## 思考2：每次可以跳一阶，跳两阶...跳n阶？



# [169. 多数元素](https://leetcode-cn.com/problems/majority-element/)（同 [剑指39](https://leetcode-cn.com/problems/shu-zu-zhong-chu-xian-ci-shu-chao-guo-yi-ban-de-shu-zi-lcof/)）

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

## 思路1：排序

先对数组进行排序，因为多数元素的出现次数超过 n/2 ，所以排序后的数组的第 n/2 元素即为要找的元素。

## 思路2：哈希表

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

# [191. 位1的个数](https://leetcode-cn.com/problems/number-of-1-bits/) （同 [剑指15](https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/)）

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

# [239. 滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/) （同 [剑指59](https://leetcode-cn.com/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof/)）

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

# [69. x 的平方根](https://leetcode-cn.com/problems/sqrtx/)

实现 int sqrt(int x) 函数。

计算并返回 x 的平方根，其中 x 是非负整数。

由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

**示例 1:**

```
输入: 4
输出: 2
```

**示例 2:**

```
输入: 8
输出: 2
说明: 8 的平方根是 2.82842..., 
     由于返回类型是整数，小数部分将被舍去。
```

## 二分查找

查找满足 k * k <= x 的 k 的最大值。

```java
class Solution {
    public int mySqrt(int x) {
        int l = 0, r = x, res = -1;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if ((long) mid * mid <= x) {	// 转换成long，防止溢出
                res = mid;
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
        return res;
    }
}
```

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

# [153. 寻找旋转排序数组中的最小值](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)

已知一个长度为 n 的数组，预先按照升序排列，经由 1 到 n 次 旋转 后，得到输入数组。例如，原数组 nums = [0,1,2,4,5,6,7] 在变化后可能得到：

* 若旋转 4 次，则可以得到 [4,5,6,7,0,1,2]
* 若旋转 7 次，则可以得到 [0,1,2,4,5,6,7]

注意，数组 [a[0], a[1], a[2], ..., a[n-1]] 旋转一次 的结果为数组 [a[n-1], a[0], a[1], a[2], ..., a[n-2]] 。

给你一个元素值 互不相同 的数组 nums ，它原来是一个升序排列的数组，并按上述情形进行了多次旋转。请你找出并返回数组中的 最小元素 。

**示例 1：**

```
输入：nums = [3,4,5,1,2]
输出：1
解释：原数组为 [1,2,3,4,5] ，旋转 3 次得到输入数组。
```

**示例 2：**

```
输入：nums = [4,5,6,7,0,1,2]
输出：0
解释：原数组为 [0,1,2,4,5,6,7] ，旋转 4 次得到输入数组。
```

**示例 3：**

```
输入：nums = [11,13,15,17]
输出：11
解释：原数组为 [11,13,15,17] ，旋转 4 次得到输入数组。
```

**提示：**

* n == nums.length
* 1 <= n <= 5000
* -5000 <= nums[i] <= 5000
* nums 中的所有整数 互不相同
* nums 原来是一个升序排序的数组，并进行了 1 至 n 次旋转

## 二分查找

**考虑数组中的最后一个元素 x：**在最小值右侧的元素（不包括最后一个元素本身），它们的值一定都严格小于 x ；而在最小值左侧的元素，它们的值一定都严格大于 x 。

将中点元素 nums[mid] 与最右元素 nums[high] 比较：

* nums[mid] < nums[high] ，说明 nums[mid] 是最小值右侧的元素，最小值在左侧，下一次查找区间为左半部分。
* nums[mid] > nums[high] ，说明 nums[mid] 是最小值左侧的元素，最小值在右侧，下一次查找区间为右半部分。
* 由于数组不包含重复元素，并且只要当前的区间长度不为 1 ，mid 就不会与 high 重合；如果当前区间长度为 1 ，就可以结束二分查找。因此不会存在 nums[mid] = nums[high] 的情况。


```java
class Solution {
    public int findMin(int[] nums) {
        // 二分法
        int low = 0;
        int high = nums.length - 1;
        while (low < high) {
            int mid = low + (high - low) / 2;
            if (nums[mid] > nums[high]) {
                low = mid + 1;
            } else {
                high = mid;
            }
        }
        return nums[low];
    }
}
```

* 时间复杂度：O(logn)
* 空间复杂度：O(1)

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

![image-20210409142534030](G:\doc\Note\LeetCode Note.assets\image-20210409142534030.png)

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

* 时间复杂度：O(n) ，需要对链表的每个节点进行反转操作。

* 空间复杂度：O(n) ，空间复杂度主要取决于递归调用的栈空间，最多为 n 层。

# [148. 排序链表](https://leetcode-cn.com/problems/sort-list/)

给你链表的头结点 head ，请将其按 **升序** 排列并返回 排序后的链表 。

**进阶：**

* 你可以在 O(n log n) 时间复杂度和常数级空间复杂度下，对链表进行排序吗？

**示例1：**

```
输入：head = [4,2,1,3]
输出：[1,2,3,4]
```

**示例 2：**

```
输入：head = [-1,5,3,4,0]
输出：[-1,0,3,4,5]
```

**示例3：**

```
输入：head = []
输出：[]
```

**提示：**

- 链表中节点的数目在范围 `[0, 5 * 104]` 内
- `-105 <= Node.val <= 105`

## 归并排序

### 自顶向下归并排序（递归）

```java
class Solution {
    public ListNode sortList(ListNode head) {
        /* * * * * * * * * * * *
         *  使用递归的归并排序  *
         * * * * * * * * * * * */
        if (head == null || head.next == null) return head;

        // 寻找中点，节点为奇数个时为中点，节点为偶数个时为左侧中间点
        ListNode fast = head.next, slow = head;
        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
        }

        ListNode temp = slow.next;
        slow.next = null;
        // 归并
        ListNode left = sortList(head);
        ListNode right = sortList(temp);
        ListNode result = merge(left, right);
        return result;
    }

    // 按升序合并两个链表（LC 21.合并两个有序链表）
    private ListNode merge(ListNode left, ListNode right) {
        ListNode dummyNode = new ListNode(0);
        ListNode l = left, r = right, temp = dummyNode;

        while (l != null && r != null) {
            if (l.val <= r.val) {
                temp.next = l;
                l = l.next;
            } else {
                temp.next = r;
                r = r.next;
            }
            temp = temp.next;
        }
        if (l != null) {
            temp.next = l;
        } else if (r != null) {
            temp.next = r;
        }
        return dummyNode.next;
    }
}
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
```

* 时间复杂度：O(nlogn)
* 空间复杂度：O(logn)，递归调用的栈空间。

### 自底向上归并排序（非递归）

* 遍历链表，计算总长度length，用 subLength 表示每次需要排序的子链表的长度，初始时 subLength=1。
* 每次将链表拆分成若干个长度为 subLength 的子链表（最后一个子链表的长度可以小于 subLength），按照每两个子链表一组进行合并，合并后即可得到若干个长度为 subLength×2 的有序子链表（最后一个子链表的长度可以小于 subLength×2）。
* 将 subLength 的值加倍，重复第 2 步，对更长的有序子链表进行合并操作，直到有序子链表的长度大于或等于 length，整个链表排序完毕。

```java
class Solution {
    public ListNode sortList(ListNode head) {
		/* * * * * * * * * * * *
         * 不使用递归的归并排序 *
         * * * * * * * * * * * */
        if (head == null || head.next == null) return head;
        int length = 0;
        ListNode node = head;
        // 统计链表长度
        while (node != null) {
            length++;
            node = node.next;
        }

        ListNode dummyNode = new ListNode(0, head);
        for (int subLength = 1; subLength < length; subLength *= 2) {
            ListNode prev = dummyNode, curr = dummyNode.next;
            while (curr != null) {
                ListNode left = curr;
                for (int i = 1; i < subLength && curr.next != null; i++) {
                    curr = curr.next;
                }
                ListNode right = curr.next;
                curr.next = null;
                curr = right;
                for (int i = 1; i < subLength && curr != null && curr.next != null; i++) {
                    curr = curr.next;
                }
                ListNode next = null;
                if (curr != null) {
                    next = curr.next;
                    curr.next = null;
                }

                ListNode merged = merge(left, right);
                prev.next = merged;
                while (prev.next != null) {
                    prev = prev.next;
                }
                curr = next;
            }
        }
        return dummyNode.next;
    }
}
```

* 时间复杂度：O(nlogn)
* 空间复杂度：O(1)

# [42. 接雨水](https://leetcode-cn.com/problems/trapping-rain-water/)

给定 *n* 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

**示例 1：**

![image-20210416205009008](G:\doc\Note\LeetCode Note.assets\image-20210416205009008.png)

```
输入：height = [0,1,0,2,1,0,1,3,2,1,2,1]
输出：6
解释：上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。
```

**示例 2：**

```
输入：height = [4,2,0,3,2,5]
输出：9
```

**提示：**

- `n == height.length`
- `0 <= n <= 3 * 104`
- `0 <= height[i] <= 105`

## 暴力解法

对于数组中的每个元素，下雨后水能达到的最高位置，等于两边最大高度的较小值减去当前高度的值。

```java
class Solution {
    public int trap(int[] height) {
        int res = 0;
        for (int i = 1; i < height.length - 1; i++) {
            int maxLeft = 0, maxRight = 0;
            for (int j = i; j >= 0; j--) {  // 往左寻找左边最大高度
                maxLeft = Math.max(maxLeft, height[j]);
            }
            for (int j = i; j < height.length; j++) {    // 往右寻找右边最大高度
                maxRight = Math.max(maxRight, height[j]);
            }
            res += Math.min(maxLeft, maxRight) - height[i];		// 当前位置的雨水：两边最大高度的较小值减去当前高度的值
        }
        return res;
    }
}
```

* 时间复杂度：O(n^2^)
* 空间复杂度：O(1)

## 动态编程

在暴力方法中，我们仅仅为了找到最大值每次都要向左和向右扫描一次。我们可以存储寻找过的最大值。

```java
class Solution {
    public int trap(int[] height) {
		if (height == null || height.length == 0) return 0;
        int res = 0;
        int size = height.length;
        int[] maxLeft = new int[size];		// 保存左边最大值
        int[] maxRight = new int[size];		// 保存右边最大值
        maxLeft[0] = height[0];
        for (int i = 1; i < size; i++) {
            maxLeft[i] = Math.max(height[i], maxLeft[i - 1]);	// i处的左边最大值在i-1处最大值与height[i]中产生
        }
        maxRight[size - 1] = height[size - 1];
        for (int i = size - 2; i >= 0; i--) {
            maxRight[i] = Math.max(height[i], maxRight[i + 1]);		// i处的右边最大值在i+1处最大值与height[i]中产生
        }
        for (int i = 1; i < size - 1; i++) {
            res += Math.min(maxLeft[i], maxRight[i]) - height[i];
        }
        return res;
    }
}
```

* 时间复杂度：O(n)
* 空间复杂度：O(n)

## 双指针

* 当从左向右处理 left 指针时，左边的最大值 leftMax 对它而言是可信的，但 rightMax 是不可信的，rightMax 未必是 left 指针右边的最大值。
* 当从右向左处理 right 指针时，同理，右边的最大值 rightMax 对它而言是可信的，但 leftMax 是不可信的。
* 对于 left 指针，它的左边的最大值一定为 leftMax , 它的右边的最大值大于等于 rightMax 。
* 当 leftMax < rightMax 时，我们可以去处理 left 指针，通过 leftMax 就可以计算出当前存多少水，无论 rightMax 多大都不会影响结果，计算完 left 向右移动一位。
* 反之，我们处理 right 指针，通过 rightMax 计算当前存多少水，然后 right 向左移动一位。

```java
class Solution {
    public int trap(int[] height) {
        int ans = 0;
        int left = 0, right = height.length - 1;
        int leftMax = 0, rightMax = 0;
        while (left < right) {
            leftMax = Math.max(leftMax, height[left]);
            rightMax = Math.max(rightMax, height[right]);
            if (leftMax < rightMax) {
                ans += leftMax - height[left];
                ++left;
            } else {
                ans += rightMax - height[right];
                --right;
            }
        }
        return ans;
    }
}
```

* 时间复杂度：O(n)
* 空间复杂度：O(1)

# [437. 路径总和 III](https://leetcode-cn.com/problems/path-sum-iii/)

给定一个二叉树，它的每个结点都存放着一个整数值。

找出路径和等于给定数值的路径总数。

路径不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。

二叉树不超过1000个节点，且节点数值范围是 [-1000000,1000000] 的整数。

**示例：**

```
root = [10,5,-3,3,2,null,11,3,-2,null,1], sum = 8

      10
     /  \
    5   -3
   / \    \
  3   2   11
 / \   \
3  -2   1

返回 3。和等于 8 的路径有:

1.  5 -> 3
2.  5 -> 2 -> 1
3.  -3 -> 11
```

## 前缀和 + 回溯

> 前缀和：从根到当前节点的路径上，所有节点的元素的和（包含当前元素）。

若处于同一路径上的节点 A 和 B ，若 “节点 B 的前缀和 - 节点 A 的前缀和 = target”，则说明节点 A 和 B 之间的元素之和为 target ，即节点 A 和 B 之间的这段路径为满足题目要求的路径。

可利用回溯遍历整棵树，并用上述方法统计出满足要求的路径数量。

```java
class Solution {
    public int pathSum(TreeNode root, int targetSum) {     
        // 保存路径中的前缀和，key是前缀和, value是大小为key的前缀和出现的次数
        Map<Integer, Integer> prefixSumMap = new HashMap<>();
        prefixSumMap.put(0, 1);     // 处理某条路径的 currsum 恰好等于 targetSum 的情况
        return recur(root, prefixSumMap, targetSum, 0);  
    }

    private int recur(TreeNode root, Map<Integer, Integer> prefixSumMap, int targetSum, int currSum) {
        if (root == null) return 0;
        int res = 0;
        currSum += root.val;	// 从根到当前节点的前缀和

        // 若Map中前缀和为currSum - targetSum的出现次数大于0，说明中间存在和为targetSum的路径
        res += prefixSumMap.getOrDefault(currSum - targetSum, 0);
        
        // 保存当前路径的前缀和
        prefixSumMap.put(currSum, prefixSumMap.getOrDefault(currSum, 0) + 1);

        res += recur(root.left, prefixSumMap, targetSum, currSum);
        res += recur(root.right, prefixSumMap, targetSum, currSum);
        
        // 向上回溯，删除当前路径，恢复状态
        prefixSumMap.put(currSum, prefixSumMap.get(currSum) - 1);
        return res;
    }
}
```

* 时间复杂度：O(n) ，树的每个节点遍历一次
* 空间复杂度：O(n)

# [889. 根据前序和后序遍历构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-postorder-traversal/)

返回与给定的前序和后序遍历匹配的任何二叉树。

 `pre` 和 `post` 遍历中的值是不同的正整数。

**示例：**

```
输入：pre = [1,2,4,5,3,6,7], post = [4,5,2,6,7,3,1]
输出：[1,2,3,4,5,6,7]
```

**提示：**

* 1 <= pre.length == post.length <= 30
* pre[] 和 post[] 都是 1, 2, ..., pre.length 的排列
* 每个输入保证至少有一个答案。如果有多个答案，可以返回其中一个。

## 递归

* 前序遍历：根、左、右
* 后序遍历：左、右、根

因此前序遍历的左子树根节点为 pre[1] 。题目已知 `pre` 和 `post` 遍历中的值是不同的正整数。所以我们可以根据 pre[1] 和值找到后序遍历中左子树根节点的位置。

这样我们就能得到左、右子树的前序和后序遍历序列，并通过递归的方式利用左子树的序列构造出左子树，利用右子树的序列构造出右子树。

```java
class Solution {
    public TreeNode constructFromPrePost(int[] pre, int[] post) {
        int len = pre.length;
        if (len == 0) return null;
        TreeNode root = new TreeNode(pre[0]);
        if (len == 1) return root;

        int index = 0;  // 左子树根节点下一个位置
        for (int i = 0; i < len; i++) {     // 找到后序遍历中左子树根节点的位置，先序遍历中左子树根节点位置为1
            if (post[i] == pre[1]) {
                index = i + 1;
                break;
            }
        }
		// 利用左子树的前序和后序遍历序列构造出左子树
        root.left = constructFromPrePost(Arrays.copyOfRange(pre, 1, index + 1), Arrays.copyOfRange(post, 0, index));
        // 利用右子树的前序和后序遍历序列构造出右子树
        root.right = constructFromPrePost(Arrays.copyOfRange(pre, index + 1, len), Arrays.copyOfRange(post, index, len - 1));
        return root;
    }
}
```

* 时间复杂度：O(n^2^)
* 空间复杂度：O(n^2^)

# [72. 编辑距离](https://leetcode-cn.com/problems/edit-distance/)

给你两个单词 word1 和 word2，请你计算出将 word1 转换成 word2 所使用的最少操作数 。

你可以对一个单词进行如下三种操作：

* 插入一个字符
* 删除一个字符
* 替换一个字符

**示例 1：**

```
输入：word1 = "horse", word2 = "ros"
输出：3
解释：
horse -> rorse (将 'h' 替换为 'r')
rorse -> rose (删除 'r')
rose -> ros (删除 'e')
```

**示例 2：**

```
输入：word1 = "intention", word2 = "execution"
输出：5
解释：
intention -> inention (删除 't')
inention -> enention (将 'i' 替换为 'e')
enention -> exention (将 'n' 替换为 'x')
exention -> exection (将 'n' 替换为 'c')
exection -> execution (插入 'u')
```

## 动态规划

```java
class Solution {
    public int minDistance(String word1, String word2) {
        int len1 = word1.length();
        int len2 = word2.length();
        int[][] dp = new int[len1 + 1][len2 + 1];

        // 第一行（word1为空，将word1变为word2需要插入字符）
        for (int j = 1; j <= len2; j++) {
            dp[0][j] = j;
        }
        // 第一列（word2为空，将word1变为word2需要删除字符）
        for (int i = 1; i <= len1; i++) {
            dp[i][0] = i;
        }

        for (int i = 1; i < dp.length; i++) {
            for (int j = 1; j < dp[i].length; j++) {
                if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                    // i 和 j 指向的字符相同，不需要操作
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    // i 和 j 指向的字符不同时，可以进行三种操作：
                    // 插入：0~i 和 0~j-1 匹配，插入一个字符使其和 j 匹配，即 dp[i][j - 1] + 1
                    // 删除：0~i-1 和 0~j 匹配，删除 i ，即 dp[i - 1][j] + 1
                    // 替换：0~i-1 和 0~j-1 匹配，将 i 替换为 j ，即 dp[i - 1][j - 1] + 1
                    dp[i][j] = Math.min(Math.min(dp[i - 1][j - 1], dp[i][j - 1]), dp[i - 1][j]) + 1;
                }
            }
        }
        return dp[len1][len2];
    }
}
```

# [208. 实现 Trie (前缀树)](https://leetcode-cn.com/problems/implement-trie-prefix-tree/)

**Trie**（发音类似 "try"）或者说 **前缀树** 是一种树形数据结构，用于高效地存储和检索字符串数据集中的键。这一数据结构有相当多的应用情景，例如自动补完和拼写检查。

请你实现 Trie 类：

* Trie() 初始化前缀树对象。
* void insert(String word) 向前缀树中插入字符串 word 。
* boolean search(String word) 如果字符串 word 在前缀树中，返回 true（即，在检索之前已经插入）；否则，返回 false 。
* boolean startsWith(String prefix) 如果之前已经插入的字符串 word 的前缀之一为 prefix ，返回 true ；否则，返回 false 。

**示例：**

```
输入
["Trie", "insert", "search", "search", "startsWith", "insert", "search"]
[[], ["apple"], ["apple"], ["app"], ["app"], ["app"], ["app"]]
输出
[null, null, true, false, true, null, true]

解释
Trie trie = new Trie();
trie.insert("apple");
trie.search("apple");   // 返回 True
trie.search("app");     // 返回 False
trie.startsWith("app"); // 返回 True
trie.insert("app");
trie.search("app");     // 返回 True
```

**提示：**

* 1 <= word.length, prefix.length <= 2000
* word 和 prefix 仅由小写英文字母组成
* insert、search 和 startsWith 调用次数 总计 不超过 3 * 104 次

## Trie树

Trie树是一种专门处理字符串匹配的数据结构，用来解决在一组字符串集合中快速查找某个字符串的问题。

Trie 树的本质，就是利用字符串之间的公共前缀，将重复的前缀合并在一起。

![img](https://static001.geekbang.org/resource/image/28/32/280fbc0bfdef8380fcb632af39e84b32.jpg)

```java
class Trie {
    private TreeNode root;

    /** Initialize your data structure here. */
    public Trie() {
        root = new TreeNode('/');
    }
    
    /** Inserts a word into the trie. */
    public void insert(String word) {
        TreeNode p = root;
        for (int i = 0; i < word.length(); i++) {
            char c = word.charAt(i);
            int index = c - 'a';
            if (p.children[index] == null) {
                TreeNode node = new TreeNode(c);
                p.children[index] = node;
            }
            p = p.children[index];
        }
        p.isEndingChar = true;
    }
    
    /** Returns if the word is in the trie. */
    public boolean search(String word) {
        TreeNode p = root;
        for (int i = 0; i < word.length(); i++) {
            char c = word.charAt(i);
            int index = c - 'a';
            if (p.children[index] == null) {
                return false;
            }
            p = p.children[index];
        }
        if (p.isEndingChar == true) return true;
        else return false;
    }
    
    /** Returns if there is any word in the trie that starts with the given prefix. */
    public boolean startsWith(String prefix) {
        TreeNode p = root;
        for (int i = 0; i < prefix.length(); i++) {
            char c = prefix.charAt(i);
            int index = c - 'a';
            if (p.children[index] == null) {
                return false;
            }
            p = p.children[index];
        }
        return true;
    }

    public class TreeNode {
        public char data;
        public TreeNode[] children = new TreeNode[26];
        public boolean isEndingChar = false;
        public TreeNode(char data) {
            this.data = data;
        }
    }
}

/**
 * Your Trie object will be instantiated and called as such:
 * Trie obj = new Trie();
 * obj.insert(word);
 * boolean param_2 = obj.search(word);
 * boolean param_3 = obj.startsWith(prefix);
 */
```

