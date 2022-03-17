---
title: Palindromic substring
date: 2021-12-26 12:30:18
summary: 回文串的相关算法题
categories: algorithm
img: https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/980081ebc17e7bbd499001644140ba4.jpg
---

![2021年12月26日，毛主席诞辰128周年。西安疫情严重，西北大学学子真情流露，在操场雪地上写下众多标语，流露出对毛主席的怀念之情以及对战胜疫情回家过年的期盼。](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/980081ebc17e7bbd499001644140ba4.jpg)

# 回文串相关题目

## leecode 234. 回文链表

**示例 1：**

![img](https://assets.leetcode.com/uploads/2021/03/03/pal1linked-list.jpg)

```
输入：head = [1,2,2,1]
输出：true
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2021/03/03/pal2linked-list.jpg)

```
输入：head = [1,2]
输出：false
```

**进阶：**你能否用 `O(n)` 时间复杂度和 `O(1)` 空间复杂度解决此题？

### 思路：

找到链表**后半部分开始的节点**，反转后半部分链表。

### 找链表中点：

- 链表节点数为奇数，快指针 fast 指向最后一个节点时，慢指针 slow的位置就是中点。
- 链表节点数为偶数，快指针 fast 指向倒数第二个节点，慢指针 slow的位置就是链表**前半部分结束的节点**。

注意**循环条件**：

```java
while(fast.next != null && fast.next.next != null) {
    slow = slow.next;
    fast = fast.next.next;
}
```

找链表中点的算法 ------> **快慢指针**

代码：

```java
public boolean middleNode(ListNode head) {
        ListNode slow = head;
        ListNode fast = head;
        while(fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
}        
```



<img src="https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/1.png" alt="1" style="zoom: 50%;" />

由上图可知：

如果是奇数，链表中点就是 slow

如果是偶数，链表中点就是slow.next

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
    public boolean isPalindrome(ListNode head) {
        ListNode slow = head;
        ListNode fast = head;
        while(fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        ListNode lastHalfReversed =  reverseRecursion(slow.next);
        while(lastHalfReversed != null) {
            if(head.val != lastHalfReversed.val) {
                return false;
            }
            head = head.next;
            lastHalfReversed = lastHalfReversed.next;
        }
        return true;
    }
    
    public ListNode reverseRecursion(ListNode head) {
        if(head == null || head.next == null) {
            return head;
        }
        ListNode newNode = reverseRecursion(head.next);
        head.next.next = head;
        head.next = null;
        return newNode;
    }
}
```

### 复杂度分析

- **时间复杂度：O(n)**	其中 n 指的是链表的大小。
- **空间复杂度：O(1)**	我们只会修改原本链表中节点的指向，而在堆栈上的堆栈帧不超过 O(1)。

## leetcode 647 . 回文子串



给你一个字符串 s ，请你统计并返回这个字符串中 回文子串 的数目。

回文字符串 是正着读和倒过来读一样的字符串。

子字符串 是字符串中的由连续字符组成的一个序列。

具有不同开始位置或结束位置的子串，即使是由相同的字符组成，也会被视作不同的子串。

**示例 1：**

```
输入：s = "abc"
输出：3
解释：三个回文子串: "a", "b", "c"
```

**示例 2：**

```
输入：s = "aaa"
输出：6
解释：6个回文子串: "a", "a", "a", "aa", "aa", "aaa"
```

**提示：**

- `1 <= s.length <= 1000`
- `s` 由小写英文字母组成

### 暴力解法：

判断所有字串是否为回文串，存储最长的回文子串

代码：

```java
class Solution {
    //判断字符串是不是 回文串  对称位置的关系  i <------> len - i - 1
    public boolean isPalindromic(String s) {
        int len = s.length();
        for(int i = 0; i < len / 2; i++) {
            if(s.charAt(i) != s.charAt(len - i - 1)) {
                return false;
            }
        }
        return true;
    }

    public String longestPalindrome(String s) {
        int num = 0;
        for(int i = 0; i < s.length(); i++) {
            for(int j = i; j < s.length(); j++) {
                String subStr = s.substring(i, j + 1);
                if(isPalindromic(subStr) && subStr.length() > longest.length()) {
                    num++;
                }
            }
        }
        return num;
    }
}
```

**时间复杂度：**

用
$$
O(N^2)
$$
的时间枚举出所有的字串，然后判断字符串是否为回文串的算法 isPalindromic（）时间复杂度为 O(N)，所以暴力解法的时间复杂度为
$$
O(N^3)
$$

### **中心扩展法：**

#### **思路：**

枚举出所有的子串（看所有的字串是不是回文串）又有另一种的思路：枚举每一个**可能的回文中心**，然后用两个指针分别向左右两边拓展，当两个指针指向的元素相同的时候就拓展，否则停止拓展。

偶数的子串(通过偶数的所有子串，找出偶数的回文子串)不能通过单个向两边扩展得到，比如 ababa中的字串 abab无法通过任何一个字符向两边扩展得到。

所以中心点不能只有单个字符构成，还要包括两个字符，比如上面这个子串 `abab`，就可以由中心点 `ba` 扩展一次得到。

#### 分析结论：

所以最终的中心点有 `2 * len - 1` 个，分别是 `len` 个单字符和 `len - 1` 个双字符。

如果上面看不太懂的话，还可以看看下面几个问题：

- 为什么有 2 * len - 1 个中心点？
  - aba 有5个中心点，分别是 a、b、c、ab、ba
  - abba 有7个中心点，分别是 a、b、b、a、ab、bb、ba
- 什么是中心点？
  - 中心点即 left 指针和 right 指针初始化指向的地方，可能是一个也可能是两个
- 为什么不可能是三个或者更多？
  - 因为 3 个可以由 1 个扩展一次得到，4 个可以由两个扩展一次得到

再次强调：如果回文长度是奇数，那么回文中心是一个字符；如果回文长度是偶数，那么中心是两个字符。

我们不妨写一组出来观察观察，假设 n = 4（abba），我们可以把可能的回文中心列出来：

| 编号(0 ~ 2 n - 1) | 回文中心 | 回文中心左起始位置 | 回文中心右起始位置 |
| :---------------: | :------: | :----------------: | :----------------: |
|         0         |    a     |         0          |         0          |
|         1         |    ab    |         0          |         1          |
|         2         |    b     |         1          |         1          |
|         3         |    bb    |         1          |         2          |
|         4         |    b     |         2          |         2          |
|         5         |    ba    |         2          |         3          |
|         6         |    a     |         3          |         3          |

单字符中心：n 个

双字符中心： n - 1 个

#### 代码：

```java
class Solution {
    public int countSubstrings(String s) {
        //回文字串最少是 s.length() -----> 也就是单个字符的个数
        int substrings = 0;
        //以单个字符为中心的情况（也就是回文子串长度是奇数的情况） n种情况
        for(int i = 0; i < s.length(); i++) {
            int left = i;
            int right = i;
            //注意left和right的取值范围
            while(left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
                substrings++;
                left--;
                right++;
            } 
        }
        //以双字符为中心的情况（也就是回文字串长度是偶数的情况） n - 1种情况 
        for(int i = 0; i < s.length() - 1; i++) {
            int left = i;
            int right = i + 1;
            while(left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
                substrings++;
                left--;
                right++;
            } 
        }
        return substrings;
    }
}
```

#### **复杂度分析**

- 时间复杂度：O(n^2)
- 空间复杂度：O(1)

## leetcode 5. 最长回文子串

### 暴力解法：

#### 代码：

```java
class Solution {
    //判断字符串是不是 回文串  对称位置的关系  i <------> len - i - 1
    public boolean isPalindromic(String s) {
        int len = s.length();
        for(int i = 0; i < len / 2; i++) {
            if(s.charAt(i) != s.charAt(len - i - 1)) {
                return false;
            }
        }
        return true;
    }

    public String longestPalindrome(String s) {
        String longest = "";
        for(int i = 0; i < s.length(); i++) {
            for(int j = i; j < s.length(); j++) {
                String subStr = s.substring(i, j + 1);
                if(isPalindromic(subStr) && subStr.length() > longest.length()) {
                    longest = subStr;
                }
            }
        }
        return longest;
    }
}
```

#### 时间复杂度:

用O(N^2)的时间枚举出所有的字串，然后判断字符串是否为回文串的算法 isPalindromic（）时间复杂度为 O(N)，所以暴力解法的时间复杂度为O(N^3)

### 中心扩展法：

同leetcode 645

#### 代码：

```java
class Solution {
    public String longestPalindrome(String s) {
        if(s == null || s.length() == 0) {
            return "";
        }
        int longest = 0;
        String longestStr = "";
        //以单个字符为中心的情况（也就是回文子串长度是奇数的情况） n种情况
        for(int i = 0; i < s.length(); i++) {
            // left = right 的时候，此时回文中心是一个字符，回文串的长度是奇数
            int left = i;
            int right = left;
            //注意left和right的取值范围
            while(left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
                int subStrLen = right - left + 1;
                if(subStrLen > longest) {
                    longest = subStrLen;
                    longestStr = s.substring(left, right + 1);
                }
                left--;
                right++;
            } 
        }
        //以双字符为中心的情况（也就是回文字串长度是偶数的情况） n - 1种情况 
        for(int i = 0; i < s.length() - 1; i++) {
            // right = left + 1 的时候，此时回文中心是一个空隙，回文串的长度是偶数
            int left = i;
            int right = left + 1;
            while(left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
                int subStrLen = right - left + 1;
                if(subStrLen > longest) {
                    longest = subStrLen;
                    longestStr = s.substring(left, right + 1);
                }
                left--;
                right++;
            } 
        }
        return longestStr;
    }
}
```

#### 复杂度分析

- 时间复杂度：O(n^2)，其中 n是字符串的长度。长度为 1和 2的回文中心分别有 n和 n−1 个，每个回文中心最多会向外扩展 O(n)次。

- 空间复杂度：O(1)

