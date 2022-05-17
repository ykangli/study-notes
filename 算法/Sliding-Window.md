---
title: Sliding Window
date: 2021-12-28 13:15:56
img: https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/50ae292c8e7f6f31e1489e3cfacd326.jpg
categories: algorithm
---

# 滑动窗口的相关题型

##  [leetcode 1. 两数之和](https://leetcode-cn.com/problems/two-sum/)

给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target  的那 两个 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。

示例 1：

```
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
```

示例 2：

```
输入：nums = [3,2,4], target = 6
输出：[1,2]
```

示例 3：

```
输入：nums = [3,3], target = 6
输出：[0,1]
```

提示：

- 2 <= nums.length <= 104
- -109 <= nums[i] <= 109
- -109 <= target <= 109

- 只会存在一个有效答案

进阶：你可以想出一个时间复杂度小于 O(n2) 的算法吗？

### 暴力解法：

#### 思路分析：

枚举数组中的每一个数 `x`，寻找数组中是否存在 `target - x`。

当我们使用遍历整个数组的方式寻找 `target - x` 时，需要注意到每一个位于 `x` 之前的元素都已经和 `x` 匹配过，因此不需要再进行匹配，所以我们只需要在 `x` 后面的元素中寻找 `target - x`。

代码：

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int right = 0;
        int[] result = new int[2];
        for(int i = 0; i < nums.length; i++) {
            int difference = target - nums[i];
            right = i + 1;
            while(right < nums.length) {
                if(difference == nums[right]) {
                    result[0] = i;
                    result[1] = right;
                    return result;
                }
                right++;
            }
        }
        return result;
    }
}
```

#### 复杂度分析

- 时间复杂度：O(N^2)，其中 N 是数组中的元素数量。最坏情况下数组中任意两个数都要被匹配一次。

- 空间复杂度：O(1)。

### 哈希表解法：

#### 思路分析：

暴力解法时间复杂度较高的原因是寻找 target - x 的时间复杂度过高。因此，我们需要一种更优秀的方法，能够快速寻找数组中是否存在目标元素。如果存在，我们需要找出它的索引。

我们可以利用HashMap，如果哈希表中无该元素，就将每遍历到的元素加入哈希表。比如：当 nums = [2, 3, 4, 5, 6, 7]， target = 9。

最多当遍历到 7 ，哈希表中已经存入 2，可以 O(1)地寻找 `target - x`。

#### 代码：

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        HashMap<Integer, Integer> map = new HashMap<>();
        int right = 0;
        int[] result = new int[2];
        for(int i = 0; i < nums.length; i++) {
            int difference = target - nums[i];
            if(map.get(difference) != null) {
                result[0] = i;
                result[1] = map.get(difference);
                return result;
            }
            map.put(nums[i], i);
        }
        return result;
    }
}
```

#### 复杂度分析

- 时间复杂度：O(N)，其中 N 是数组中的元素数量。对于每一个元素 x，我们可以 O(1) 地寻找 target - x。

- 空间复杂度：O(N)，其中 N 是数组中的元素数量。主要为哈希表的开销。

## [leetcode 15. 三数之和](https://leetcode-cn.com/problems/3sum/)

给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有和为 0 且不重复的三元组。

注意：答案中不可以包含重复的三元组。

示例 1：

```
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
```

示例 2：

```
输入：nums = []
输出：[]
```

示例 3：

```
输入：nums = [0]
输出：[]
```


提示：

- `0 <= nums.length <= 3000`
- `-105 <= nums[i] <= 105`

### 思路分析:

如果我们直接使用三重循环枚举三元组，会得到 O(N^3)个满足题目要求的三元组（其中 N是数组的长度）**时间复杂度至少为 O(N^3)**。在这之后，我们还需要使用哈希表进行去重操作，得到不包含重复三元组的最终答案，又消耗了大量的空间。这个做法的时间复杂度和空间复杂度都很高，因此我们要换一种思路来考虑这个问题。

拿这个 nums 数组来举例，首先将数组排序，然后有一层for循环，k 从下标0的地方开始，同时定一个下标 i = k+1 的位置上，定义下标 j 在数组结尾的位置上(j = nums.length - 1)。

依然还是在数组中找到 abc 使得a + b +c =0，我们这里相当于 a = nums[k] ,b = nums[i] ,c = nums[j]。

接下来如何移动left 和right呢， 如果nums[i] + nums[left] + nums[right] > 0 就说明 此时三数之和大了，因为数组是排序后了，所以right下标就应该向左移动，这样才能让三数之和小一些。

如果 nums[i] + nums[left] + nums[right] < 0 说明 此时 三数之和小了，i 就向右移动，才能让三数之和大一些，直到 i 与 j 相遇为止。

### 双指针法思路

 固定 3个指针中最左（最小）数字的指针 k，双指针 i，j 分设在数组索引 (k, len(nums)) 两端，通过双指针交替向中间移动，记录对于每个固定指针 k 的所有满足 nums[k] + nums[i] + nums[j] == 0 的 i,j 组合：

1. 当 nums[k] > 0 时直接break跳出：因为 nums[j]  >=  nums[i]  >=  nums[k]  > 0，即 3个数字都大于 0，则三数之和必然无法等于 0，结束循环。

2. 当 k > 0且nums[k] == nums[k - 1]时即跳过此元素nums[k]：因为**已经将 nums[k - 1] 的所有组合加入到结果中**，本次双指针搜索只会得到重复组合。

3. i，j 分设在数组索引  (k, len(nums)) 两端，当i < j时循环计算s = nums[k] + nums[i] + nums[j]，并按照以下规则执行双指针移动：

   - 当s < 0时，说明nums[i] 太小，i 右移；

   - 当s > 0时，说明nums[j] 太大，j 左移；

   - 当s == 0时，记录组合[k, i, j]至 res，**执行i += 1和j -= 1**，并跳过所有重复的nums[i] 和 nums[j]，防止记录到重复组合。

其实第二个和第三个数就是**滑动窗口**。

### 代码

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        if(nums == null || nums.length < 3) {
            return res;
        }
        Arrays.sort(nums);
        for(int k = 0; k < nums.length; k++) {
            if(nums[k] > 0) {
                break;
            }
            //k进行去重  注意去重条件    nums[k] == nums[k + 1]是错误的！！！！
            if(k > 0 && nums[k] == nums[k - 1]) {
                continue;
            }
            int i = k + 1;
            int j = nums.length - 1;
            while(i < j) {
                int sum = nums[k] + nums[i] + nums[j];
                if(sum < 0) {
                    i++;
                } else if(sum > 0) {
                    j--;           
                } else {
                    res.add(Arrays.asList(nums[k], nums[i], nums[j]));
                    //i,j同时减小，过程中去除重复的i,j   
                   //注意++i效率更高，++i是直接对于原对象进行自增，不用生成临时变量
                    //而且不需要最后再进行一次 i++ 到不重复的位置去
                    while(i < j && nums[i] == nums[++i]) {
                    }
                    while(i < j && nums[j] == nums[--j]) {
                    }
                }
            }
        }
        return res;
    }
}
```

++i 和 i++**两者参与运算时的区别就是：**

1. a=i++ , a 返回原来的值a=i,i=i+1;
   a=++i , a 返回加1后的值,a=i+1,i=i+1。
   也就是**i++是先赋值，然后再自增；++i是先自增，后赋值。**
2. 第二个区别就是： i++ 不能作为左值，而++i可以。

**两者效率在某些情况下仍有不同！**

当我们考虑自定义类的时候，就不一样了。

i++是先用临时对象保存原来的对象，然后对原对象自增，再返回临时对象，不能作为左值；**++i是直接对于原对象进行自增**，然后返回原对象的引用，可以作为左值。

由于要生成临时对象，i++需要调用两次拷贝构造函数与析构函数（将原对象赋给临时对象一次，临时对象以值传递方式返回一次）；

**++i由于不用生成临时变量**，且以引用方式返回，故没有构造与析构的开销，**效率更高**。

所以在使用类等自定义类型的时候，应尽量使用++i。

![image-20211230163506590](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20211230163506590.png)

## [leetcode 18. 四数之和](https://leetcode-cn.com/problems/4sum/)

给你一个由 n 个整数组成的数组 nums ，和一个目标值 target 。请你找出并返回满足下述全部条件且不重复的四元组 [nums[a], nums[b], nums[c], nums[d]] （若两个四元组元素一一对应，则认为两个四元组重复）：

- 0 <= a, b, c, d < n

- a、b、c 和 d **互不相同**
- nums[a] + nums[b] + nums[c] + nums[d] == target

你可以按 **任意顺序** 返回答案 。

示例 1：

```
输入：nums = [1,0,-1,0,-2,2], target = 0
输出：[[-2,-1,1,2],[-2,0,0,2],[-1,0,0,1]]
```

示例 2：

```
输入：nums = [2,2,2,2,2], target = 8
输出：[[2,2,2,2]]
```


提示：

- 1 <= nums.length <= 200
- -109 <= nums[i] <= 109
- -109 <= target <= 109

### 思路

暴力解法为O（N^4），数组排序后，最里面两层利用滑动窗口，可将时间复杂度将为0(N ^ 3)

### 代码

```java
class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        List<List<Integer>> res = new LinkedList<>();
        if(nums == null || nums.length < 4) {
            return res;
        }
        Arrays.sort(nums);
        for(int m = 0; m < nums.length; m++) {
            //不能用这里的去重，因为target是任意值
            //if(nums[m] > target) {
            //    break;
            //}
            if(m > 0 && nums[m] == nums[m - 1] ) {
                continue;
            }
            for(int n = m + 1; n < nums.length; n++) {
                //不能用这里的去重，因为target是任意值
                //例： nums[m] == -3, nums[n] == -2 ,target = -6
                // if(nums[m] + nums[n] > target) {
                //     break;
                // }
                if(n > m + 1 && nums[n] == nums[n - 1]) {
                    continue;
                }
                int i = n + 1;
                int j = nums.length - 1;
                while(i < j) {
                    int sum = nums[m] + nums[n] + nums[i] + nums[j];
                    if(sum < target) {
                        i++;
                    } else if(sum > target) {
                        j--;
                    } else {
                        res.add(Arrays.asList(nums[m] , nums[n] , nums[i] , nums[j]));
                        //去重
                        while(i < j && nums[i] == nums[++i]) {
                        }
                        //去重
                        while(i < j && nums[j] == nums[--j]) {
                        }
                    } 
                }
            }
        }
        return res;
    }
}
```

## [leetcode 3. 无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

给定一个字符串 s ，请你找出其中不含有重复字符的 最长子串 的长度。

示例 1:

```
输入: s = "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

示例 2:

```
输入: s = "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```

示例 3:

```
输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```

示例 4:

```
输入: s = ""
输出: 0
```

**提示：**

- `0 <= s.length <= 5 * 104`
- `s` 由英文字母、数字、符号和空格组成

### 暴力解法

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if(s.length() == 0 || s == null) {
            return 0;
        }
        int res = 1;
        for(int i = 0; i < s.length(); i++) {
            int j = i + 1;
            HashMap<Character, Character> map = new HashMap<>();
            map.put(s.charAt(i), s.charAt(i));
            while(j < s.length() && map.get(s.charAt(j)) == null) {
                map.put(s.charAt(j), s.charAt(j));
                j++;
            }
            res = Math.max(res, j - i);
        }
        return res;
    }
}
```

### 滑动窗口解法

思想：

暴力解法时间复杂度是O(N^2)，使用滑动窗口可遍历一次数组完成，使得时间复杂度将为O（N）

滑动窗口的右边界不断的右移，只要没有重复的字符，就持续向右扩大窗口边界。一旦出现了重复字符，就需要缩小左边界，直到重复的字符移出了左边界，然后继续移动滑动窗口的右边界。以此类推，每次移动需要计算当前长度，并判断是否需要更新最大长度，最终最大的值就是题目中的所求。

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if(s.length() == 0 || s == null) {
            return 0;
        }
        int len = s.length();
        int result = 0;
        int right = -1;  
        //set用来存储当前无重复字串都有哪些唯一的字符
        HashSet<Character> set = new HashSet<>();
        for(int left = 0; left < len; left++) {
            //每次left移动位置，都要去除set中原left位置对应的元素，因为set中始终存储的是当前不重复字串
            if(left != 0) {
                set.remove(s.charAt(left - 1));
            }
            //只要没有重复的字符，就持续向右扩大窗口边界
            while(right + 1 < len && !set.contains(s.charAt(right + 1))) {
                right++;
                //向set中添加
                set.add(s.charAt(right));
            }
            result = Math.max(result, right - left + 1);
        }
        return result;
    }
}
```

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if(s.length() == 0) {
            return 0;
        }
        int left = 0;
        int right = 0;
        int result = 1;
        HashSet<Character> set = new HashSet<>();
        int length = s.length();
        while(right < length) {
            //只要没有重复的字符，就持续向右扩大窗口边界
            if(!set.contains(s.charAt(right))) {
                set.add(s.charAt(right));
                result = Math.max(result, right - left + 1);
                right++;
            }
            //一旦出现了重复字符，就需要缩小左边界，直到重复的字符移出了左边界
            while(right < length && set.contains(s.charAt(right))) {
                set.remove(s.charAt(left));
                left++;
            }
        }
        return result;
    }
}
```

## [leetcode 209. 长度最小的子数组](https://leetcode-cn.com/problems/minimum-size-subarray-sum/)

给定一个含有 `n` 个正整数的数组和一个正整数 `target` **。**

找出该数组中满足其和 `≥ target` 的长度最小的 **连续子数组** `[numsl, numsl+1, ..., numsr-1, numsr]` ，并返回其长度**。**如果不存在符合条件的子数组，返回 `0` 。

**示例 1：**

```
输入：target = 7, nums = [2,3,1,2,4,3]
输出：2
解释：子数组 [4,3] 是该条件下的长度最小的子数组。
```

**示例 2：**

```
输入：target = 4, nums = [1,4,4]
输出：1
```

**示例 3：**

```
输入：target = 11, nums = [1,1,1,1,1,1,1,1]
输出：0
```

**提示：**

- `1 <= target <= 109`
- `1 <= nums.length <= 105`
- `1 <= nums[i] <= 105`

**进阶：**

- 如果你已经实现 `O(n)` 时间复杂度的解法, 请尝试设计一个 `O(n log(n))` 时间复杂度的解法。

### 思路

子数组想到是否能利用`滑动窗口` ？？？

这一题的解题思路是用滑动窗口。在滑动窗口 [i,j]之间不断往后移动，如果总和小于 s，就扩大右边界 j，不断加入右边的值，直到 sum > s，之后再缩小 i 的左边界，不断缩小直到 sum < s，这时候右边界又可以往右移动。以此类推。

### 代码

```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int left = 0;
        int sum = 0;
        int result = Integer.MAX_VALUE;
        //扩大窗口
        for(int right = 0; right < nums.length; right++) {
            sum += nums[right];
            //缩小窗口
            while(sum >= target) {
                result = Math.min(result, right - left + 1);
                sum -= nums[left];
                left++;
            } 
        }
        //有无找到
        return result == Integer.MAX_VALUE ? 0 : result;
    }
}
```

# 代码框架



```java
class Solution {
    public String minWindow(String s, String t) {
         // 记录t 以及 滑动窗口window中 字符与个数的映射关系
        HashMap<Character, Integer> window_map = new HashMap<>();
        HashMap<Character, Integer> t_map = new HashMap<>();
        for (int i = 0; i < t.length(); i++) {
            char c1 = t.charAt(i);
            t_map.put(c1, t_map.getOrDefault(c1, 0) + 1);
        }
        // 双指针, 
        int left = 0;
        int right = 0;
        int count = 0;
        // 用于更新满足的窗口window的长度,如果是len一直是MAX_VALUE，说明没有满足的串
        int len = Integer.MAX_VALUE;
        // 用于记录window串的起始位置，则返回 s[start, len]
        int start = 0;

        while (right < s.length()) {
            char c = s.charAt(right);
            right++;
            // 只要c是t中出现的字符就更新
            if (t_map.containsKey(c)) {
                window_map.put(c, window_map.getOrDefault(c, 0) + 1);
                // 更新c字符出现的次数
                if (window_map.get(c).equals(t_map.get(c))) {
                    count++;
                }
            }
            // System.out.println(window_map); 
            // ----------------------------------
            // 收缩window的长度
            while (count == t_map.size()) {
                // 更新并记录window的长度,以及window的起始位置start
                if (right - left < len) {
                    start = left;
                    len = right - left;
                }

                char d = s.charAt(left);
                left++;

                if (t_map.containsKey(d)) {
                    if (window_map.get(d).equals(t_map.get(d))) {
                        count--;
                    }
                    window_map.put(d, window_map.get(d) - 1);
                }
            }
        } 
        return len == Integer.MAX_VALUE ? "" : s.substring(start, start + len);
    }
}
```

