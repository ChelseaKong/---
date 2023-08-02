学习Link：https://programmercarl.com/%E5%93%88%E5%B8%8C%E8%A1%A8%E7%90%86%E8%AE%BA%E5%9F%BA%E7%A1%80.html

## 1. 哈希表理论基础

1. 根据 关键码的值 而直接进行访问的数据结构
   - 数组是一张哈希表，关键码是数组的索引下标，通过下标直接访问数组中的元素
2. 解决的问题：**用来快速判断一个元素是否出现在集合里**
   - 例子：查询一个名字是否在学校的表里
   - 牺牲了空间，换取了时间。需要额外的数组，set或者map来存放数据。
   - 枚举：O(N)，哈希表：O(1)
3. 哈希函数
   - index = hashFunction(name) // hashFunction = hashCode(name) % tableSize （ hashCode 把名字转化为数值）
   - 如果 hashCode 得到的值大于 tableSize，则会对值进行取模。问题：多个名字同时映射到同一个索引下标
4. 哈希碰撞
   - 多个名字同时映射到同一个索引下标的现象
   - 两种解决方法：
     - 拉链法
       - 发生冲突的元素都被存储在 链表 里，这样可以通过同一个索引找到多个名字
       - 关键在于选择适当的 tableSize。既不会因为数组空值而浪费大量内存，也不会因为链表太长而浪费太多查找时间
     - 线性探测法
       - tableSize > dataSize，依靠哈希表中的空位来解决问题
       - 发生冲突的位置，向下找空位放冲突的。
5. 常见的三种哈希结构
   - 数组
   - set
   - map
6. C++中的set
   - set：红黑树（平衡二叉搜索树），有序，不可以重复，不能修改数值，查找 O(log N)，增删 O(log N)
   - multiset：红黑树，有序，可重复，不能修改数值，查找 O(log N)，增删 O(log N)
   - **unordered_set：哈希表，无序，不可以重复，不能修改数值，查找 O(1)，增删 O(1)**
7. C++中的map
   - map：红黑树，key有序，key不可以重复，key不能修改数值，查找 O(log N)，增删O(log N)
   - multimap：红黑树，key有序，key可重复，key不能修改数值，查找 O(log N)，增删O(log N)
   - **unordered_map：哈希表，key无序，key不可以重复，key不能修改数值，查找 O(1)，增删 O(1)**

## 2. 242. Valid Anagram - easy

Given the head of a linked list and an integer val, remove all the nodes of the linked list that has Node.val == val, and return the new head.

Example 1:

`Input: head = [1,2,6,3,4,5,6], val = 6`

`Output: [1,2,3,4,5]`

- The number of nodes in the list is in the range [0, $10^4$].
- 1 <= Node.val <= 50
- 0 <= val <= 50

### 思路：在head前设置一个preHead，用来防止head->val=val。

```
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        ListNode *pre = new ListNode(), *cur = head;
        ListNode *preHead = pre;
        pre->next = head;
        cur = head;
        while ( cur != nullptr ) {
            if ( cur->val == val ) {
                pre->next = cur->next;
            } else {
                pre = cur;
            }
            cur = cur->next;
        }
        return preHead->next;
    }
};
```

### 学习：

1. 两种方法
   - 直接用**原来的链表**进行删除操作
     
     移除头节点和移除其他节点的操作是不一样的！
     
   - 设置**一个虚拟头节点**再进行删除操作

     dummyHead

2. 代码
   - 原来的链表 时间复杂度：O(N) 空间复杂度：O(1)

     ```
     class Solution {
     public:
        ListNode* removeElements(ListNode* head, int val) {
           // 删除头结点
           while (head != NULL && head->val == val) { // 注意这里不是if
               ListNode* tmp = head;
               head = head->next;
               delete tmp; // 释放
           }

           // 删除非头结点
           ListNode* cur = head;
           while (cur != NULL && cur->next!= NULL) {
               if (cur->next->val == val) {
                   ListNode* tmp = cur->next;
                   cur->next = cur->next->next;
                   delete tmp;
               } else {
                   cur = cur->next;
               }
           }
           return head;
        }
     };
     ```
     
   - 一个虚拟头节点 时间复杂度：O(N) 空间复杂度：O(1)

     ```
     class Solution {
     public:
        ListNode* removeElements(ListNode* head, int val) {
           ListNode* dummyHead = new ListNode(0); // 设置一个虚拟头结点
           dummyHead->next = head; // 将虚拟头结点指向head，这样方面后面做删除操作
           ListNode* cur = dummyHead;
     
           while (cur->next != NULL) {
               if (cur->next->val == val) {
                   ListNode* tmp = cur->next;
                   cur->next = cur->next->next;
                   delete tmp;
               } else {
                   cur = cur->next;
               }
           }
           head = dummyHead->next;
           delete dummyHead;
           return head;
        }
     };
     ```

## 3. 707. Design Linked List - medium

Link: https://leetcode.cn/problems/design-linked-list/

- 0 <= index, val <= 1000
- Please do not use the built-in LinkedList library.
- At most 2000 calls will be made to get, addAtHead, addAtTail, addAtIndex and deleteAtIndex.

### 思路：要记得定义ListNode！使用dummyHead

```
class MyLinkedList {
public:
    struct ListNode {
        int val;  
        ListNode *next;  
        ListNode(int x) : val(x), next(NULL) {}
    };
    ListNode *dummyHead;
    int len;

    MyLinkedList() {
        dummyHead = new ListNode(0);
        len = 0;
    }
    
    int get(int index) {
        if ( index < 0 || index >= len ) {
            return -1;
        }
        ListNode *cur = dummyHead;
        for ( int i=0; i<=index; i++ ) { // 找到index这个节点
            cur = cur->next;
        }
        return cur->val;
    }
    
    void addAtHead(int val) {
        ListNode *node = new ListNode(val);
        node->next = dummyHead->next;
        dummyHead->next = node;
        len ++;
    }
    
    void addAtTail(int val) {
        ListNode *cur = dummyHead;
        ListNode *node = new ListNode(val);
        for ( int i=0; i<len; i++ ) { // index从0开始，则 i<len，找到最后一个节点
            cur = cur->next;
        }
        cur->next = node;
        len ++;
    }
    
    void addAtIndex(int index, int val) {
        if ( index < len ) {
            ListNode *cur = dummyHead;
            ListNode *node = new ListNode(val);
            for ( int i=0; i<index; i++ ) { //找到index前面一个节点，在index这里插入
                cur = cur->next;
            }
            node->next = cur->next;
            cur->next = node;
            len ++;
        } else if ( index == len ) {
            addAtTail(val);
        } else if ( index < 0 ) {
            addAtHead(val);
        } else {
            return ;
        }
    }
    
    void deleteAtIndex(int index) {
        if ( index < 0 || index >= len || len == 0 ) {
            return ;
        }
        
        ListNode *cur = dummyHead;
        for ( int i=0; i<index; i++ ) { //找到index前面一个节点，在index这里删除
            cur = cur->next;
        }
        cur->next = cur->next->next;
        len --;
    }
};
```

### 学习：

1. 代码不一样的点 - 时间复杂度: 涉及 index 的相关操作为 O(index), 其余为 O(1)。空间复杂度: O(n)

   - for 变成 while

   ```
   while ( index -- ) { // int get(int index)
      cur = cur->next; 
   }

   while(cur->next != nullptr){ // void addAtTail(int val)
      cur = cur->next;
   }
   ```

   - void addAtIndex(int index, int val)

   ```
   // 在第index个节点之前插入一个新节点，例如index为0，那么新插入的节点为链表的新头节点。
   // 如果index 等于链表的长度，则说明是新插入的节点为链表的尾结点
   // 如果index大于链表的长度，则返回空
   // 如果index小于0，则在头部插入节点
   void addAtIndex(int index, int val) {
        if(index > len) return;
        if(index < 0) index = 0;        
        LinkedNode* newNode = new LinkedNode(val);
        LinkedNode* cur = _dummyHead;
        while( index-- ) {
            cur = cur->next;
        }
        newNode->next = cur->next;
        cur->next = newNode;
        len++;
   }
   ```

   - void deleteAtIndex(int index)

   ```
   LinkedNode* tmp = cur->next;
   cur->next = cur->next->next;
   delete tmp;
   //delete命令指示释放了tmp指针原本所指的那部分内存，
   //被delete后的指针tmp的值（地址）并非就是NULL，而是随机值。也就是被delete后，
   //如果不再加上一句tmp=nullptr,tmp会成为乱指的野指针
   //如果之后的程序不小心使用了tmp，会指向难以预想的内存空间
   tmp=nullptr;
   ```
   
2. 打印链表
   ```
   void printLinkedList() {
        LinkedNode* cur = _dummyHead;
        while (cur->next != nullptr) {
            cout << cur->next->val << " ";
            cur = cur->next;
        }
        cout << endl;
   }
   ```

## 4. 206. Reverse Linked List - easy

Given the head of a singly linked list, reverse the list, and return the reversed list.

Example 1:

`Input: head = [1,2,3,4,5]`

`Output: [5,4,3,2,1]`

- The number of nodes in the list is the range [0, 5000].
- -5000 <= Node.val <= 5000

Follow up: A linked list can be reversed either iteratively or recursively. Could you implement both?

### 思路：无

```

```

### 学习：

1. 
