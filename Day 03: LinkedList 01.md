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
