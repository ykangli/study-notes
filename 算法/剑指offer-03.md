---

title: 刷题 12/24
date: 2021-12-24 09:58:19
categories: algorithm
img: https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/e4afd8b644160bcf8fbadce95cbd962.jpg
summary: 疫情中的人们都在努力的生活着。

---

## 剑指offer 03. 数组中重复的数字

![](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20211224095946844.png)

### 一.遍历暴力解法 ( 利用HashSet )

利用HashSet, 每遍历一个就去Set集合中查询，如果有就说明前面已经存在；若没有将其存入Set。 

代码：

```java
class Solution {
    public int findRepeatNumber(int[] nums) {
        HashSet<Integer> set = new HashSet<>();
        for(int i = 0; i < nums.length; i++) {
            if(set.contains(nums[i])) {
                return nums[i];
            } else {
                set.add(nums[i]);
            }  
        }
        return -1;
    }
}
```

**notes**： 牢记`HashSet`的两个函数

1. **add( )**                  如果Set集合中存在该元素，则返回 false
2. **contains ( )**         如果Set集合中存在该元素，则返回 false

- **时间复杂度 O(N)：** 遍历数组使用 O(N) ，HashSet 添加与查找元素皆为 O(1)。

- **空间复杂度 O(N)：** HashSet 占用 O(N)  大小的额外空间。

### 二. 原地交换 

实际中，可能要求降低空间复杂度。那我们就不能新建 HashSet，利用Set集合的唯一性来实现。那我们就得想，有没有办法仅仅使用当前的数组 nums[ ]来模拟Set。

我们仔细观察该题目，发现 ------->  `在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内`

可遍历数组并通过交换操作，**使元素与索引值 一 一对应**（即 nums[i] = inums[i]=i ）。因而，就能通过索引映射对应的值，起到与Set等价的作用。

![image-20211224104020538](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20211224104020538.png)

遍历中，第一次遇到数字 xx 时，将其交换至索引 xx 处；而当第二次遇到数字 xx 时，一定有 nums[x] = xnums[x]=x ，此时即可得到一组重复数字。

**算法步骤：**

一、遍历数组 nums[ ]：

- 若 nums[i] == i， 说明元素与索引值对应，继续下一个位置。
- 若 nums[nums[i]] == nums[i]， 说明 索引nums[i]处和索引 i 处的元素值都是 nums[i]，找到重复的值，那么返回 nusm[i].
- 否则，交换 索引 i 处和 索引 nums[i] 处的元素值，也就是把 元素nums[i], 放到索引 nums[i]处。使得元素值与索引对应。

二、 遍历完成未找到，返回-1 （本题有重复元素，一定能找到）

代码：

```java
class Solution {
    public int findRepeatNumber(int[] nums) {
        int i = 0;
        while(i < nums.length) {
            //元素与索引值对应，继续下一个位置
            if(nums[i] == i) {
                i++;
                continue;
            }
            //找到重复的值 
            if(nums[nums[i]] == nums[i]) {
                return nums[i];
            }
            //交换元素位置，使得元素值与索引对应
            int temp =  nums[i];
            nums[i] = nums[temp];
            nums[temp] = temp;
        }
        return -1;
    }
}
```

- **时间复杂度 O(N):**    遍历数组使用*O*(*N*) ，每轮遍历的判断和交换操作使用*O*(1) 。

- **空间复杂度 O(1) ：** 使用常数复杂度的额外空间。

### 要求时间复杂度O(1) 的话，怎么实现？？？？？？？

## 剑指 Offer 30. 包含min函数的栈

<img src="https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20211224110825312.png" alt="image-20211224110825312"  />

### 分析：

普通栈的 `push()` 和 `pop()` 函数的复杂度为 *O*(1) ；而获取栈最小值 `min()` 函数需要遍历整个栈，复杂度为 *O*(*N*) 。

### 难点：

那么本题要解决的就是 **将 `min()` 函数复杂度降为 *O*(1) ，可通过建立辅助栈实现**。

### 具体操作：

- **数据栈 A**：  栈 A用于存储所有元素，保证入栈 push() 函数、出栈 pop() 函数、获取栈顶 top() 函数的正常逻辑。
- **辅助栈 B** ： 栈 B中存储栈 A中所有 非严格降序（相等也行） 的元素，则栈 A 中的最小元素始终对应栈 B的栈顶元素，即 min() 函数只需返回栈 B 的栈顶元素即可，可达到min（）的时间复杂度为O(N)的目的。

### 代码：

```java
class MinStack {
    //A保存所有元素，B保存非严格单调递减的元素（为了使得B栈顶就是最小值，时间复杂度为O（1））
    Deque<Integer> A, B;

    /** initialize your data structure here. */
    public MinStack() {
        this.A = new LinkedList<>();
        this.B = new LinkedList<>();
    }
    
    public void push(int x) {
        A.push(x);
        if(B.isEmpty() || B.peek() >= x) {
            B.push(x);
        }
    }
    
    public void pop() {
        if(A.peek().equals(B.peek())) {
            B.pop();
        }
        A.pop();
    }
    
    public int top() {
        return A.peek();
    }
    
    public int min() {
        return B.peek();
    }
}

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack obj = new MinStack();
 * obj.push(x);
 * obj.pop();
 * int param_3 = obj.top();
 * int param_4 = obj.min();
 */
```

### 复杂度分析：

- **时间复杂度 O(1) ：** push( ), pop( ), top( ), min( ) 四个函数的时间复杂度均为常数级别。
- **空间复杂度 O(N) ：** 当共有 N 个待入栈元素时，辅助栈 BB 最差情况下存储 N 个元素，使用 O(N） 额外空间。

## 剑指offer 10 - II. 青蛙跳台的问题

<img src="https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20211224170310559.png" alt="image-20211224170310559" style="zoom:80%;" />

### 递归三部曲：

1. **找base case**，也就是**找整个递归的终止条件：递归应该在什么时候结束？**

2. **找返回值**：应该给上一级返回什么信息？

3. **本级递归应该做什么：**在这一级递归中，应该完成什么任务？

一般想清楚**本级递归应该做什么**就能解决递归！

在本题中，设跳上 n级台阶有 f(n)种跳法。在所有跳法中，青蛙的最后一步只有两种情况： **跳上 1 级或 2 级台阶**。

所以本级递归应该完成 f(n) = f(n -1) + f(n - 2) ，就是把两种情况的各自有多少种情况加起来。

### 代码：

```java
class Solution {
    //map用来记忆算过的f（n）,加快递归速度，不用去重复递归计算
    HashMap<Integer, Integer> map = new HashMap<>();
    public int numWays(int n) {
        if(map.get(n) != null) {
            return map.get(n);
        }
        int result = 0;
        //base case
        if(n == 1 || n == 0) {
            return 1;
        }
        //本级递归种要做的事情
        result = (numWays(n - 2)  + numWays(n -1)) % 1000000007;
        map.put(n, result);
        return result;
    }
}
```

## leetcode 24. 两两交换链表中的结点 

https://leetcode-cn.com/problems/swap-nodes-in-pairs/

![image-20211224201958367](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20211224201958367.png)

这种链表中的k个一组交换 -----> 递归   相似的还有` leetcode 25. K个一组反转链表` https://leetcode-cn.com/problems/reverse-nodes-in-k-group/

**base case**: 只有一个节点或为空

**本级递归要做的事情**：

head， head.next， swapPairs（head.next.next）这三个节点中，将head和head.next交换

### 代码：

```java
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
class Solution {
    public ListNode swapPairs(ListNode head) {
        //base case
        if(head == null || head.next == null) {
            return head;
        }
        //本级递归
        ListNode second = head.next;
        head.next = swapPairs(head.next.next);
        second.next = head;
        return second;
    }
}
```





