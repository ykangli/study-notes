---
title: Spiral Matrix
date: 2022-01-11 19:45:47
categories: algorithm
img: https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/ccff416fa39887c938d36fec8e490e1861813d3bba7836eda941426f13420759-Picture1.png
---

## [54. 螺旋矩阵](https://leetcode-cn.com/problems/spiral-matrix/)

给你一个 `m` 行 `n` 列的矩阵 `matrix` ，请按照 **顺时针螺旋顺序** ，返回矩阵中的所有元素。

**Example 1:**

![image-20220111194853048](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220111194853048.png)

```
Input: matrix = [[1,2,3],[4,5,6],[7,8,9]]
Output: [1,2,3,6,9,8,7,4,5]
```

**Example 2:**

![image-20220111195003770](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220111195003770.png)

```
Input: matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
Output: [1,2,3,4,8,12,11,10,9,5,6,7]
```

**提示:**

- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= m, n <= 10`
- `-100 <= matrix[i][j] <= 100`

### 思路

参考`[59. 螺旋矩阵 II]`，模拟顺时针顺序，依次将数字填入结果链表`result`中

### 代码

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        int m = matrix.length;
        int n = matrix[0].length;
        int top = 0, bottom = m - 1, left = 0, right = n - 1;
        int num = 1;
        List<Integer> result = new LinkedList<>();
        while(result.size() <= m * n) {
            //从左到右
            for(int i = left; i <= right; i++) {
                result.add(matrix[top][i]);
                if(result.size() == m * n) {
                    return result;
                }
            }
            top++;
            //从上往下
            for(int i = top; i <= bottom; i++) {
                result.add(matrix[i][right]);
                if(result.size() == m * n) {
                    return result;
                }
            }
            right--;
            //从右往左
            for(int i = right; i >= left; i--) {
                result.add(matrix[bottom][i]);
                if(result.size() == m * n) {
                    return result;
                }
            }
            bottom--;
            //从下往后上
            for(int i = bottom; i >= top; i--) {
                result.add(matrix[i][left]);
                if(result.size() == m * n) {
                    return result;
                }
            }
            left++;
        }
        return result;
    }
}
```



## [59. 螺旋矩阵 II](https://leetcode-cn.com/problems/spiral-matrix-ii/)

给你一个正整数 `n` ，生成一个包含 `1` 到 `n2` 所有元素，且元素按顺时针顺序螺旋排列的 `n x n` 正方形矩阵 `matrix` 。

**示例 1：**

![image-20220111194853048](https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/image-20220111194853048.png)

```
输入：n = 3
输出：[[1,2,3],[8,9,4],[7,6,5]]
```

**示例 2：**

```
输入：n = 1
输出：[[1]]
```

**提示：**

- `1 <= n <= 20`

### 思路

- 生成一个 `n × n `空矩阵 mat，随后模拟整个向内环绕的填入过程：
  1. 定义当前左右上下边界` l,r,t,b`，初始值 num = 1，迭代终止值 `tar = n * n`；
  2. 当 `num <= tar` 时，始终按照 从左到右 从上到下 从右到左 从下到上 填入顺序循环，每次填入后：
     - 执行 num += 1：得到下一个需要填入的数字；
     - 更新边界：例如从左到右填完后，上边界 t += 1，相当于上边界向内缩 1。
  3. 使用`num <= tar`而不是`l < r || t < b`作为迭代条件，是为了解决当n为奇数时，矩阵中心数字无法在迭代过程中被填充的问题。

- 最终返回 mat 即可。

<img src="https://ykangliblog.oss-cn-beijing.aliyuncs.com/article/ccff416fa39887c938d36fec8e490e1861813d3bba7836eda941426f13420759-Picture1.png" alt="Picture1.png" style="zoom:67%;" />

### 代码

```java
class Solution {
    public int[][] generateMatrix(int n) {
        int top = 0, bottom = n - 1, left = 0, right = n - 1; 
        int[][] result = new int[n][n];
        int num = 1;
        int target = n * n;
        while(num <= target) {
            //从左到右
            for(int i = left; i <= right; i++) {
                result[top][i] = num++;
            }
            top++;
            //从上往下
            for(int i = top; i <= bottom; i++) {
                result[i][right] = num++;
            }
            right--;
            //从右往左
            for(int i = right; i >= left; i--) {
                result[bottom][i] = num++;
            }
            bottom--;
            //从下往后上
            for(int i = bottom; i >= top; i--) {
                result[i][left] = num++;
            }
            left++;
        }
        return result;
    }
}
```

## [剑指 Offer 29. 顺时针打印矩阵](https://leetcode-cn.com/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/)

难度简单335

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

注意：本题与主站 54 题相同：https://leetcode-cn.com/problems/spiral-matrix/
