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

滑动窗口：**只用一个for循环**。

3. **双指针法 - 快慢指针**
   - 快指针：寻找新数组的元素，新数组就是不含有目标元素的数组
   - 慢指针：指向更新新数组下标的位置

```
// 时间复杂度：O(n)
// 空间复杂度：O(1)
class Solution {  // 没有改变元素的相对位置
public:
    int removeElement(vector<int>& nums, int val) {
        int slowIndex = 0;
        for (int fastIndex = 0; fastIndex < nums.size(); fastIndex++) {
            if (val != nums[fastIndex]) {
                nums[slowIndex++] = nums[fastIndex];
            }
        }
        return slowIndex;
    }
};
```

3. 相向双指针法 - 一开始想到的做法，但实现过程不一样

```
/**
* 相向双指针方法，基于元素顺序可以改变的题目描述改变了元素相对位置，确保了移动最少元素
* 时间复杂度：O(n)
* 空间复杂度：O(1)
*/
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int leftIndex = 0;
        int rightIndex = nums.size() - 1;
        while (leftIndex <= rightIndex) {
            // 找左边等于val的元素
            while (leftIndex <= rightIndex && nums[leftIndex] != val){
                ++leftIndex;
            }
            // 找右边不等于val的元素
            while (leftIndex <= rightIndex && nums[rightIndex] == val) {
                -- rightIndex;
            }
            // 将右边不等于val的元素覆盖左边等于val的元素
            if (leftIndex < rightIndex) {
                nums[leftIndex++] = nums[rightIndex--];
            }
        }
        return leftIndex;   // leftIndex一定指向了最终数组末尾的下一个元素
    }
};
```
