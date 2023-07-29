学习Link：https://programmercarl.com/%E9%93%BE%E8%A1%A8%E7%90%86%E8%AE%BA%E5%9F%BA%E7%A1%80.html#%E9%93%BE%E8%A1%A8%E7%9A%84%E7%B1%BB%E5%9E%8B

## 1. 链表理论基础

1. 链表的类型
   - 单链表
   - 双链表
   - 循环链表（解决约瑟夫环问题）

2. 链表的存储方式

   链表在内存中不是连续分布的。

   是通过指针域的指针 链接在内存中各个节点。

   所以链表的节点是散乱分布在内存中的某地址上，分配机制取决于操作系统的内存管理。

3. 链表的定义

    ```
   // 单链表
    struct ListNode {
        int val;  
        ListNode *next;  
        ListNode(int x) : val(x), next(NULL) {}  // 节点的构造函数
    };
    ```

    **节点的构造函数**
   - 通过自己定义的构造函数 初始化节点

     ```
     ListNode *head = new ListNode(5);
     ```

   - 不定义构造函数的话，在C++是可以的。C++默认生成一个构造函数，但是不会初始化任何成员变量。
   
     **初始化的时候不能直接给变量赋值！**

     使用默认构造函数 初始化节点

     ```
     ListNode *head = new ListNode();
     head->val = 5;
     ```
     
4. 链表的操作
   - 删除节点 O(1)

     C++最好手动释放删除节点的内存。Java，Python则有自己的内存回收机制，不用手动释放。
     
   - 添加节点 O(1)
   - 查找节点 O(N)

5. 数组和链表对比

   | | 插入/删除的时间复杂度| 查询的时间复杂度| 使用场景|
   |:--- |:--- |:--- |:--- |
   | 数组 |O(N)|O(1)| 数据量固定，多查询，少增删|
   | 链表 |O(1)|O(N)| 数据量不固定，多增删，少查询|

   数组在定义的时候，长度就是固定的。如果要改变数组的长度，需要重新定义一个新的数组。

   链表的长度可以不固定，可以动态增删。

## 2. 203. Remove Linked List Elements - easy

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

## 2. 203. Remove Linked List Elements - easy

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

## 2. 203. Remove Linked List Elements - easy

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
