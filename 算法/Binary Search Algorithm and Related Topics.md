---
title: Binary Search Algorithm and Related Topics
date: 2022-01-08 10:38:43
img: https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/R-C.jpg
categories: algorithm
---

### 二分查找算法的要求

- 1.必须采用**顺序存储**结构
- 2.必须按关键字大小有序排列

### 算法思想

首先，将表中间位置记录的关键字与查找关键字比较，如果两者相等，则查找成功；否则利用中间位置记录将表分成前、后两个子表，如果中间位置记录的关键字大于查找关键字，则进一步查找前一子表，否则进一步查找后一子表。
重复以上过程，直到找到满足条件的记录，使查找成功，或直到子表不存在为止，此时查找不成功。

第一种写法，我们定义 target 是在一个在左闭右闭的区间里，**也就是[left, right] （这个很重要非常重要）**。

区间的定义这就决定了二分法的代码应该如何写，**因为定义target在[left, right]区间，所以有如下两点：**

- while (left <= right) 要使用 **<=** ，因为left == right是有意义的 （当数组个数为**偶数**时，就会出现这种情况），所以使用 <=
- if (nums[middle] > target) right 要赋值为 middle - 1，因为当前这个nums[middle]一定不是target，那么接下来要查找的左区间结束下标位置就是 middle - 1

例如在数组：1,2,3,4,7,9,10中查找元素2，如图所示：

<img src="https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/20210311153055723.jpg" alt="704.二分查找" style="zoom:80%;" />

### 代码

```java
class Solution {
    public int search(int[] nums, int target) {
        if(target < nums[0] || target > nums[nums.length - 1]) {
            return -1;
        }
        int low = 0;
        int high = nums.length - 1;
        while(low <= high) {
            int mid = (low + high) / 2;
            //找到直接返回
            if(nums[mid] == target) {
                return mid;
            } else if(nums[mid] < target) {//在右子表
                low = mid + 1;
            } else {//在左子表
                high = mid - 1;
            }
        }
        return -1;
    }
}
```

**二分查找最重要的就是边界问题**     

while（low <= high）{} 

### 算法分析

![image-20220108134734989](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220108134734989.png)

## [leetcode 35. 搜索插入位置](https://leetcode-cn.com/problems/search-insert-position/)

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

请必须使用时间复杂度为 **O(log n)** 的算法。

示例 1:

```
输入: nums = [1,3,5,6], target = 5
输出: 2
```

示例 2:

```
输入: nums = [1,3,5,6], target = 2
输出: 1
```

示例 3:

```
输入: nums = [1,3,5,6], target = 7
输出: 4
```

示例 4:

```
输入: nums = [1,3,5,6], target = 0
输出: 0
```

示例 5:

```
输入: nums = [1], target = 0
输出: 0
```

### 代码

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        //先二分查找，利用pos记录 mid 最后的位置
        int pos = -1;
        int low = 0;
        int high = nums.length - 1;
        while(low <= high) {
            int mid = (low + high) / 2;
            pos = mid;
            if(nums[mid] < target) {
                low = mid + 1;
            } else if(nums[mid] > target) {
                high = mid - 1;
            } else {
                return mid;
            }
        }
        //根据最后pos的位置来确定不存在元素的插入位置
        if(nums[pos] < target) {
            return pos + 1;
        } else if(pos == 0) {
            return 0;
        } else {
            return pos;
        }
    }
}
```

## [leetcode 34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 target，返回 [-1, -1]。

进阶：

你可以设计并实现时间复杂度为 O(log n) 的算法解决此问题吗？


示例 1：

```
输入：nums = [5,7,7,8,8,10], target = 8
输出：[3,4]
```

示例 2：

```
输入：nums = [5,7,7,8,8,10], target = 6
输出：[-1,-1]
```

示例 3：

```
输入：nums = [], target = 0
输出：[-1,-1]
```


提示：

- 0 <= nums.length <= 105

- -109 <= nums[i] <= 109
- nums 是一个非递减数组
- -109 <= target <= 109

### 最先想到的解法思路

利用二分法找到第一个符合的位置，然后向两边扩展，但如果整个数组都是目标值的活，时间复杂度应该是**O（N）**

#### 代码

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        if(nums == null || nums.length == 0) {
            return new int[]{-1, -1};
        }
        int pos = -1;
        int low = 0;
        int high = nums.length - 1;
        int left = 0;
        int right = 0;
        while(low <= high) {
            int mid = (low + high) / 2;
            pos = mid;
            if(nums[mid] < target) {
                low = mid + 1;
            } else if(nums[mid] > target) {
                high = mid - 1;
            } else {
                break;
            }
        }
        if(nums[pos] != target) {
            return new int[]{-1, -1};
        } else {
            left = pos;
            right = pos;
            //找左边界
            while(left - 1 >= 0 && nums[left - 1] == target) {
                left--;
            }
            //找右边界
            while(right + 1 <= nums.length - 1 && nums[right + 1] == target) {
                right++;
            }
            return new int[]{left, right};
        }
    }
}
```

### 优化解法

这一题是经典的二分搜索变种题。二分搜索有 4 大基础变种题：

1. 查找第一个值等于给定值的元素
2. 查找最后一个值等于给定值的元素
3. 查找第一个大于等于给定值的元素
4. 查找最后一个小于等于给定值的元素

这一题的解题思路可以分别利用变种 1 和变种 2 的解法就可以做出此题。或者用一次变种 1 的方法，然后循环往后找到最后一个与给定值相等的元素。不过后者这种方法可能会使时间复杂度下降到 O(n)，因为有可能数组中 n 个元素都和给定元素相同。(4 大基础变种的实现见代码)

#### 代码

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        return new int[]{firstElement(nums, target), lastElement(nums, target)};
    }

    public int firstElement(int[] nums, int target) {
        int low = 0;
        int high = nums.length - 1;
        while(low <= high) {
            int mid = (low + high) / 2;
            if(nums[mid] < target) {
                low = mid + 1;
            } else if(nums[mid] > target) {
                high = mid - 1;
            } else {
                if(mid == 0 || nums[mid - 1] != target) {
                    return mid;
                }
                //说明在当前mid 的左边还有符合的元素，那么就去左边继续利用二分查找
                high = mid - 1;
            }
        }
        return -1;
    }

    public int lastElement(int[] nums, int target) {
        int low = 0;
        int high = nums.length - 1;
        while(low <= high) {
            int mid = (low + high) / 2;
            if(nums[mid] < target) {
                low = mid + 1;
            } else if(nums[mid] > target) {
                high = mid - 1;
            } else {
                if(mid == nums.length - 1 || nums[mid + 1] != target) {
                    return mid;
                }
                //说明在当前mid 的左边还有符合的元素，那么就去左边继续利用二分查找
                low = mid + 1;
            }
        }
        return -1;
    }
}
```

#### 算法分析

左右边界均是利用二分查找找到的，所以时间复杂度为 O（logN）

## [leetcode 69. Sqrt(x)](https://leetcode-cn.com/problems/sqrtx/)

给你一个非负整数 x ，计算并返回 x 的 算术平方根 。

由于返回类型是整数，结果只保留 整数部分 ，小数部分将被 舍去 。

注意：不允许使用任何内置指数函数和算符，例如 pow(x, 0.5) 或者 x ** 0.5 。

示例 1：

```
输入：x = 4
输出：2
```

示例 2：

```
输入：x = 8
输出：2
解释：8 的算术平方根是 2.82842..., 由于返回类型是整数，小数部分将被舍去。
```


提示：

- 0 <= x <= 231 - 1


### 思路

利用二分查找，设结果为 `res`， 可以看作在区间【0，x】上找出最后一个res，使得 `res * res <= x`

利用二分查找最后一个小于等于 target 的元素的模板，稍加改动

### 代码

```java
class Solution {
    public int mySqrt(int x) {
        //特殊值
        if(x == 0) {
            return 0;
        }
        int pos = 0;
        int low = 1;
        int high = x;
        while(low <= high) {
            int mid = low + (high - low) / 2;
            //使用(long)强转 以及 不使用mid * mid而是用 mid > x / mid为了防止超过 int 最大值
            if((long)mid * mid <= x) {
                if(mid + 1 > x / (mid + 1)) {
                    pos = mid;
                    break;
                }
                low = mid + 1;
            } else{
                high = mid - 1;
            }
        }
        return pos;
    }
}
```

## [leetcode 367. 有效的完全平方数](https://leetcode-cn.com/problems/valid-perfect-square/)

给定一个 正整数 num ，编写一个函数，如果 num 是一个完全平方数，则返回 true ，否则返回 false 。

进阶：不要 使用任何内置的库函数，如  sqrt 。

 

示例 1：

```
输入：num = 16
输出：true
```

示例 2：

```
输入：num = 14
输出：false
```


提示：

- 1 <= num <= 2^31 - 1

### 思路

二分查找相关题目，同69题

### 代码

```java
class Solution {
    public boolean isPerfectSquare(int num) {
        int low = 0;
        int high = num;
        int pos = -1;
        while(low <= high) {
            int mid = low + (high - low) / 2;
            if((long)mid * mid == num) {
                return true;
            } else if ((long)mid * mid < num) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
        return false;
    }
}
```

### 4 大基础变种的代码实现

 [Go实现，转载自 **halfrost**《LeetCode Cookbook》](https://books.halfrost.com/leetcode/ChapterFour/0001~0099/0034.Find-First-and-Last-Position-of-Element-in-Sorted-Array/)

```go
func searchRange(nums []int, target int) []int {
	return []int{searchFirstEqualElement(nums, target), searchLastEqualElement(nums, target)}

}

// 二分查找第一个与 target 相等的元素，时间复杂度 O(logn)
func searchFirstEqualElement(nums []int, target int) int {
	low, high := 0, len(nums)-1
	for low <= high {
		mid := low + ((high - low) >> 1)
		if nums[mid] > target {
			high = mid - 1
		} else if nums[mid] < target {
			low = mid + 1
		} else {
			if (mid == 0) || (nums[mid-1] != target) { // 找到第一个与 target 相等的元素
				return mid
			}
			high = mid - 1
		}
	}
	return -1
}

// 二分查找最后一个与 target 相等的元素，时间复杂度 O(logn)
func searchLastEqualElement(nums []int, target int) int {
	low, high := 0, len(nums)-1
	for low <= high {
		mid := low + ((high - low) >> 1)
		if nums[mid] > target {
			high = mid - 1
		} else if nums[mid] < target {
			low = mid + 1
		} else {
			if (mid == len(nums)-1) || (nums[mid+1] != target) { // 找到最后一个与 target 相等的元素
				return mid
			}
			low = mid + 1
		}
	}
	return -1
}

// 二分查找第一个大于等于 target 的元素，时间复杂度 O(logn)
func searchFirstGreaterElement(nums []int, target int) int {
	low, high := 0, len(nums)-1
	for low <= high {
		mid := low + ((high - low) >> 1)
		if nums[mid] >= target {
			if (mid == 0) || (nums[mid-1] < target) { // 找到第一个大于等于 target 的元素
				return mid
			}
			high = mid - 1
		} else {
			low = mid + 1
		}
	}
	return -1
}

// 二分查找最后一个小于等于 target 的元素，时间复杂度 O(logn)
func searchLastLessElement(nums []int, target int) int {
	low, high := 0, len(nums)-1
	for low <= high {
		mid := low + ((high - low) >> 1)
		if nums[mid] <= target {
			if (mid == len(nums)-1) || (nums[mid+1] > target) { // 找到最后一个小于等于 target 的元素
				return mid
			}
			low = mid + 1
		} else {
			high = mid - 1
		}
	}
	return -1
}
```





