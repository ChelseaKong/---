学习Link：https://programmercarl.com/%E6%95%B0%E7%BB%84%E7%90%86%E8%AE%BA%E5%9F%BA%E7%A1%80.html

## 1. 数组理论基础

数组：
1. 数据类型一致
2. 索引标识元素位置，从0开始
3. 元素在内存中是连续存储的，且每个元素占用相同大小的内存。 会记下索引为0的内存地址。

数组操作：
1. 读取：时间复杂度 O（1）
2. 查找：从头往后查找，时间复杂度 O（N）
3. 插入：复杂操作，需要移动其他元素，腾出要插入的位置，再进行插入操作。 链表更好操作。
4. 删除：复杂操作，删掉元素后，数组会留下空缺，需要后面的元素进行填补。 时间复杂度O（N）（**数组的元素不能删的，只能覆盖**）

二维数组：
1. 每个元素变为一维数组。 本质上仍然是一个一维数组，会申请一段连续的空间，并记录 A[0][0] 的内存地址。

C++：
1. vector 的底层实现是array。

## 2. 704. Binary Search - easy
Given an array of integers nums which is sorted in ascending order, and an integer target, write a function to search target in nums. If target exists, then return its index. Otherwise, return -1.

You must write an algorithm with O(log n) runtime complexity.

Example 1:

`Input: nums = [-1,0,3,5,9,12], target = 9`

`Output: 4`

`Explanation: 9 exists in nums and its index is 4`

- 1 <= nums.length <= $10^4$
- $-10^4$ < nums[i], target < $10^4$
- All the integers in nums are unique.
- nums is sorted in ascending order.

### 思路：O（N）直接for循环。O（log N）则二分查找。

```
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0, right = nums.size()-1, middle = 0;
        while ( left <= right ) {
            middle = left + ( right - left ) / 2;
            if ( nums[middle] < target ) {
                left = middle + 1;
            } else if ( nums[middle] > target ) {
                right = middle - 1;
            } else {
                return middle;
            }
        }
        return -1;
    }
};
```

### 值得注意的：**边界问题**

### 学习：

1. 思路
   - 题目中：有序数组，无重复元素，都是使用二分法的前提！（如果集合是无序的，应在二分查找前，对其进行排序。）
   - 二分查找：查找集合中的索引或者元素时，都应考虑二分查找。时间复杂度 O（log N），空间复杂度  O（1）
   - 二分查找涉及的边界条件，主要是对区间的定义。区间的定义就是**不变量**。
   - **循环不变量**：在while寻找中，每一次对于边界的处理都要坚持根据区间的定义来操作。（在二分查找的过程中，保持不变量）
   - ①左闭右闭
   - ②左闭右开

2. **左闭右闭** - 时间复杂度 O（log N），空间复杂度  O（1）

定义 target 在 [ left, right ] 这个区间里。left = 0, right = nums.size()-1

- `while ( left <= right)`，left == right 是有意义的。
- `if ( nums[middle] > target ) right = middle - 1;`，当前 middle 一定不是 target，那么之后要查找的区间为 **[ left, middle - 1 ]**
- `if ( nums[middle] < target ) left = middle + 1;`，当前 middle 一定不是 target，那么之后要查找的区间为 **[ middle + 1, right ]**

3. **左闭右开** - 时间复杂度 O（log N），空间复杂度  O（1）

定义 target 在 [ left, right ) 这个区间里。left = 0, right = nums.size()

- `while ( left < right)`，left == right 没有意义的。
- `if ( nums[middle] > target ) right = middle;`，当前 middle 一定不是 target，那么之后要查找的区间为 **[ left, middle )**，即下一个查询区间不会去比较当前 middle 值
- `if ( nums[middle] < target ) left = middle + 1;`，当前 middle 一定不是 target，那么之后要查找的区间为 **[ middle + 1, right )**

## 3. 27. Remove Element - easy

Given an integer array nums and an integer val, remove all occurrences of val in nums **in-place**. The order of the elements may be changed. Then return the number of elements in nums which are not equal to val.

Consider the number of elements in nums which are not equal to val be k, to get accepted, you need to do the following things:

- Change the array nums such that the first k elements of nums contain the elements which are not equal to val. The remaining elements of nums are not important as well as the size of nums.
- Return k.

Example 1:

`Input: nums = [0,1,2,2,3,0,4,2], val = 2`

`Output: 5, nums = [0,1,4,0,3,_,_,_]`

```
Explanation: Your function should return k = 5, with the first five elements of nums containing 0, 0, 1, 3, and 4.
Note that the five elements can be returned in any order.
It does not matter what you leave beyond the returned k (hence they are underscores).
```

- 0 <= nums.length <= 100
- 0 <= nums[i] <= 50
- 0 <= val <= 100

### 思路：左右指针往中间交汇

```
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int left = 0, right = nums.size() - 1, ret = 0;
        while ( left <= right ) {
            if ( nums[left] == val ) {
                swap( nums[left], nums[right] );
                right --;
                ret ++;
            } else {
                left ++;
            }
        }
        return nums.size()-ret;
    }
};
```

### 学习：

1. 数组的元素在内存地址中是连续的，不能单独删除数组中的某个元素，只能覆盖。

2. **双指针法 - 快慢指针**
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
