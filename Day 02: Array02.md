学习Link：https://programmercarl.com/0977.%E6%9C%89%E5%BA%8F%E6%95%B0%E7%BB%84%E7%9A%84%E5%B9%B3%E6%96%B9.html

## 总结数组

1. 二分法 - 时间复杂度：O(log N) - 循环不变量原则
2. 双指针法 - 时间复杂度：O(N) - 快慢指针（数组和链表常见）
3. 滑动窗口 - 时间复杂度：O(N) - 动态调节子序列的起始位置
4. 模拟 - 循环不变量原则

## 1. 977. Squares of a Sorted Array - easy

Given an integer array nums sorted in non-decreasing order, return an array of the squares of each number sorted in non-decreasing order.

Example 1:

`Input: nums = [-4,-1,0,3,10]`

`Output: [0,1,9,16,100]`

`Explanation: After squaring, the array becomes [16,1,0,9,100].
After sorting, it becomes [0,1,9,16,100].`

- 1 <= nums.length <= $10^4$
- $-10^4$ < nums[i] < $10^4$
- nums is sorted in non-decreasing order.

Follow up: Squaring each element and sorting the new array is very trivial, could you find an O(n) solution using a different approach?

### 思路：两个左右指针，谁大放数组最后。时间复杂度 O(N)

```
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {
        int n = nums.size(), index = nums.size()-1, left = 0, right = nums.size()-1;
        vector<int> ret (n);
        while ( left <= right ) {
            if ( nums[left] * nums[left] <= nums[right] *nums[right] ) {
                ret[index] = nums[right] *nums[right];
                right --;
            } else {
                ret[index] = nums[left] * nums[left];
                left ++;
            }
            index --;
        }
        return ret;
    }
};
```

### 值得注意的：无

### 学习：

1. 更简洁的代码

vector<int> result(A.size(), 0);

for (int i = 0, j = A.size() - 1; i <= j;) { // 注意这里要i <= j，因为最后要处理两个元素

```
class Solution {
public:
    vector<int> sortedSquares(vector<int>& A) {
        int k = A.size() - 1;
        vector<int> result(A.size(), 0);
        for (int i = 0, j = A.size() - 1; i <= j;) { // 注意这里要i <= j，因为最后要处理两个元素
            if (A[i] * A[i] < A[j] * A[j])  {
                result[k--] = A[j] * A[j];
                j--;
            }
            else {
                result[k--] = A[i] * A[i];
                i++;
            }
        }
        return result;
    }
};
```

## 2. 209. Minimum Size Subarray Sum - medium ×

Given an array of positive integers nums and a positive integer target, return the **minimal length** of a 
**subarray** whose sum is greater than or equal to the target. If there is no such subarray, return 0 instead.

Example 1:

`Input: target = 7, nums = [2,3,1,2,4,3]`

`Output: 2`

`Explanation: The subarray [4,3] has a minimal length under the problem constraint.`

- 0 <= target <= $10^9$
- 1 <= nums.length <= $10^5$
- 1 <= nums[i] <= $10^4$

Follow up: If you have figured out the O(n) solution, try coding another solution of which the time complexity is O(n log(n)).

### 思路：两层for循环 - 超时了 时间复杂度：O($N^2$) 空间复杂度：O(1)

```
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int minLength = INT_MAX, subSum = 0;
        for ( int i=0; i<nums.size(); i++ ) {
            subSum = 0;
            for ( int j=i; j<nums.size(); j++ ) {
                subSum += nums[j];
                if ( subSum >= target ) {
                    minLength = min( minLength, j-i+1 );
                    break;
                } 
            }
        }
        if ( minLength == INT_MAX ) {
            minLength = 0;
        }
        return minLength;
    }
};
```

### 学习：滑动窗口

1. 滑动窗口：不断调节子序列的起始位置和终止位置，从而得出想要的结果。

暴力解法：一个for循环循环滑动窗口的起始位置，一个for循环找滑动窗口的终止位置，两个for循环完成了一个不断搜索区间的过程。

滑动窗口：**只用一个for循环**。双指针的一种！

- **窗口内是什么**
  - 和 >=target 的长度最小的连续子数组
- **如何移动起始位置**
  - 如果当前窗口值大于target，则窗口就要向前移动（缩小窗口）
- **如何移动结束位置**
  - 结束位置即遍历数组的指针，也就是for循环里的索引

**本题关键在于：如何移动起始位置**

// 动态调节滑动窗口的起始位置

**sum -= nums[i];**
    
**i++;**

2. 代码

时间复杂度：O(N) 空间复杂度：O(1)

```
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int minLength = INT_MAX;
        int sum = 0; // 滑动窗口数值之和
        int i = 0; // 滑动窗口起始位置
        for ( int j=0; j<nums.size(); j++ ) {
            sum += nums[j];
            // 注意这里使用while，每次更新 i（起始位置），并不断比较子序列是否符合条件
            while ( sum >= target ) {
                minLength = min( minLength, j-i+1 );
                // 这里体现出滑动窗口的精髓之处，不断变更i（子序列的起始位置）
                sum -= nums[i++];
            }
        }
        if ( minLength == INT_MAX ) {
            minLength = 0;
        }
        return minLength;
    }
};
```

时间复杂度：O(N)。主要看每一个元素被操作的次数。每个元素在滑动窗后进来操作一次，出去操作一次，每个元素都是被操作两次，所以时间复杂度是 2 × N，也就是O(N)。

## 3. Spiral Matrix II - medium ×

Given a positive integer n, generate an n x n matrix filled with elements from 1 to $n^2$ in spiral order.

Example 1:

`Input: n = 3`

`Output: [[1,2,3],[8,9,4],[7,6,5]]`

- 1 <= n <= 20

### 思路：没有想法

### 学习：

1. 本题是模拟过程，坚持**循环不变量原则**！

模拟顺时针画矩阵的过程：（**四条边，每条边都坚持 左闭右开**）
- 上行：从左到右
- 右列：从上到下
- 下行：从右到左
- 左列：从下到上

2. 代码

时间复杂度：O($N^2$) 空间复杂度：O(1)

```
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        vector<vector<int>> ret( n, vector<int>( n, 0 ) );
        int startX = 0, startY = 0; // 定义每循环一个圈的起始位置
        int loop = n / 2; // 每个圈循环几次，例如n为奇数3，那么loop = 1 只是循环一圈，矩阵中间的值需要单独处理
        int mid = n / 2; // 矩阵中间的位置，例如：n为3， 中间的位置就是(1，1)，n为5，中间位置为(2, 2)
        int count = 1; // 用来给矩阵中每一个空格赋值
        int offset = 1; // 需要控制每一条边遍历的长度，每次循环右边界收缩一位
        int i = 0, j = 0;
        
        while ( loop -- ) {
            i = startX;
            j = startY;

            // 下面开始的四个for就是模拟转了一圈

            // 模拟填充上行从左到右(左闭右开)
            for ( j = startY; j < n - offset; j++ ) {
                ret[startX][j] = count++;
            }

            // 模拟填充右列从上到下(左闭右开)
            for ( i = startX; i < n - offset; i++ ) {
                ret[i][j] = count ++;
            }

            // 模拟填充下行从右到左(左闭右开)
            for ( ; j > startY; j-- ) {
                ret[i][j] = count ++;
            }

            // 模拟填充左列从下到上(左闭右开)
            for ( ; i > startX; i-- ) {
                ret[i][j] = count ++;
            }

            // 第二圈开始的时候，起始位置要各自加1， 例如：第一圈起始位置是(0, 0)，第二圈起始位置是(1, 1)
            startX ++;
            startY ++;

            // offset 控制每一圈里每一条边遍历的长度
            offset += 1;
        }

        // 如果n为奇数的话，需要单独给矩阵最中间的位置赋值
        if ( n % 2 == 1 ) {
            ret[mid][mid] = count;
        }
        return ret;
    }
};
```
