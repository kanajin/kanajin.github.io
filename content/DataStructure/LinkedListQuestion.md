---
title: "链表题目本"
date: 2021-06-09T21:57:29+08:00
drend: false
categories: ["数据结构"]
enableMathJax: false
---

# 反转链表
## 题目
定义一个函数，输入一个链表的头节点，反转该链表并输出反转后链表的头节点。
## 示例
```
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
```
## 解法
此题有两种解法——迭代法和递归法，分别介绍。
### 迭代法
思路：遍历链表，在遍历过程中逐个改变所遍历到的节点的指针域，使其指向前一个节点。  
实现方法：使用三指针法。图片与解法摘自<http://c.biancheng.net/view/8105.html>
首先定义三个指针并分别命名为beg、mid、end，初始指向如下图：  
![reverselist_loop_1](/LinkedListQuestion/reverselist_loop_1.gif)  

遍历方式为：三个指针各自向后移动一个节点，直至mid指针指向最后一个节点(此时end指针指向NULL)。在指针移动前，将mid所指节点的指针域指向节点指向beg指针指向的节点，如下图  
![reverselist_loop_2](/LinkedListQuestion/reverselist_loop_2.gif)  

一开始mid指向的值改为初始beg指向(即为NULL)，然后三个指针右移。接下来同理：  
![reverselist_loop_2](/LinkedListQuestion/reverselist_loop_3.gif)  
![reverselist_loop_2](/LinkedListQuestion/reverselist_loop_4.gif)  
![reverselist_loop_2](/LinkedListQuestion/reverselist_loop_5.gif)  

最后改变head指针指向，使其与mid相同，反转完成。  
代码：
```C
struct ListNode* reverseList(struct ListNode* head){
    //如果是空链表或只有一个元素，无需操作直接返回
    if(head == NULL || head -> next == NULL){
        return head;
    }
    //定义三个指针
    struct ListNode* beg = NULL;
    struct ListNode* mid = head;
    struct ListNode* end = head -> next;
    //遍历链表
    while(1){
        //使mid指针域指向beg
        mid -> next = beg;
        if(end == NULL){
            break;
        }
        //使指针后移一位
        beg = mid;
        mid = end;
        end = end -> next;
    }
    //重新定义头结点
    head = mid;
    return head;
}
```
### 递归法
递归法可以理解成从尾节点开始，依次向前遍历，过程中改变节点指向，使其指向前一个节点.  
代码：
```C
struct ListNode* reverseList(struct ListNode* head){
    //当为空链表或抵达递归出口(尾结点)时，递归结束
    if(head == NULL || head -> next == NULL){
        return head;
    }
    //递归至尾节点，根据递归出口，此时head指针指向最后一个节点，在原本链表尾部定义一个新的头结点ptr
    struct ListNode* ptr = reverseList(head -> next);
    //使head指针指向的节点的指针域指向head
    head -> next -> next = head;
    //将head节点的指针域指空，否则会出现互指的情况
    head -> next = NULL;
    //返回新的头结点供递归使用
    return ptr;
}
```
---
# 链表中倒数第k个节点
## 题目
输入一个链表，输出该链表中倒数第k个节点。  
为了符合大多数人的习惯，本题从1开始计数，即链表的尾节点是倒数第1个节点。  
例如：一个链表有`6`个节点，从头节点开始，它们的值依次是`1、2、3、4、5、6`。这个链表的倒数第`3`个节点是值为`4`的节点。
## 示例
```
给定一个链表: 1->2->3->4->5, 和 k = 2。
返回链表 4->5。
```
## 解法
### 正向遍历法
先遍历一次链表，获得链表长度`n`，然后再遍历到第`n - k + 1`个位置，即为倒数第k个。
代码：
```C
struct ListNode* getKthFromEnd(struct ListNode* head, int k){
    struct ListNode* ptr = head;
    //遍历链表，查看长度
    int i = 0;
    while(head != NULL){
        i++;
        head = head -> next;
    }
    //算出正数第几个，正攻。
    for(int j = 0; j < i - k; j++){
        if(ptr == NULL){
            break;
        }
        ptr = ptr -> next;
    }
    return ptr;
}
```
### 双指针法
本解法参考<https://zhuanlan.zhihu.com/p/59564074>
思路为建立两个指针指向链表头部，其中一个指针遍历到第k个时，第二个指针开始和第一个指针同时遍历。这样，两个指针之间的距离始终为k-1，当第一个指针遍历到链表尾部时，第二个指针刚好遍历到倒数第k个位置。  
代码：
```C
struct ListNode* getKthFromEnd(struct ListNode* head, int k){
    if(head == NULL){
        return NULL;
    }
    if(k == 0){
        return NULL;
    }
    struct ListNode* ptr_1 = head;
    struct ListNode* ptr_2 = head;
    for(int i = 0; i < k; i++){
        if(ptr_1 == NULL){
            return NULL;
        }
        ptr_1 = ptr_1 -> next;
    }
    while(ptr_1 != NULL){
        ptr_1 = ptr_1 -> next;
        ptr_2 = ptr_2 -> next;
    }
    return ptr_2;
}
```
---
# 合并两个排序的链表
## 题目
输入两个递增排序的链表，合并这两个链表并使新链表中的节点仍然是递增排序的。
## 示例
输入：1->2->4, 1->3->4  
输出：1->1->2->3->4->4

## 解法
与归并排序相同的思路，代码也类似。根据链表特性调整了冗余。
代码：
```C
struct ListNode* mergeTwoLists(struct ListNode* l1, struct ListNode* l2){
    if(l1 == NULL){
        return l2;
    }
    if(l2 == NULL){
        return l1;
    }
    struct ListNode* l3;
    struct ListNode* l4;
    if(l1 -> val <= l2 -> val){
        l3 = l1;
        l1 = l1 -> next;
    }
    else{
        l3 = l2;
        l2 = l2 -> next;
    }
    l4 = l3;
    while(1){
        if(l1 == NULL || l2 == NULL){
            break;
        }
        if(l1 -> val <= l2 -> val){
            l3 -> next = l1;
            l3 = l1;
            l1 = l1 -> next;
        }
        else{
            l3 -> next = l2;
            l3 = l2;
            l2 = l2 -> next;
        }
    }
    if(l1 != NULL){
        l3 -> next = l1;
    }
    if(l2 != NULL){
        l3 -> next = l2;
    }
    return l4;
}
```
---
# 链表的中间结点
## 题目
给定一个头结点为`head`的非空单链表，返回链表的中间结点。  
如果有两个中间结点，则返回第二个中间结点。
## 示例
1. 输入：`[1,2,3,4,5]`  
输出：此列表中的结点`3`(序列化形式：`[3,4,5]`)  
返回的结点值为`3`。 (测评系统对该结点序列化表述是 `[3,4,5]`)。  
注意，我们返回了一个 ListNode 类型的对象 ans，这样：  
ans.val = 3, ans.next.val = 4, ans.next.next.val = 5, 以及 ans.next.next.next = NULL.
2. 输入：`[1,2,3,4,5,6]`  
输出：此列表中的结点 4 (序列化形式：`[4,5,6]`)  
由于该列表有两个中间结点，值分别为 3 和 4，我们返回第二个结点。

## 解法
### 单指针先遍历法
先遍历一次链表求出链表长度n，然后再求出中点值。  
代码:  
```C
struct ListNode* middleNode(struct ListNode* head){
    int n = 0;
    struct ListNode* ans = head;
    while(head != NULL){
        n++;
        head = head -> next;
    }
    for(int i = 1; i < (n / 2) + 1; i++){
        ans = ans -> next;
    }
    return ans;
}
```
### 快慢指针法
*快慢指针是极度重要的思路！*  
定义两个指针分别遍历链表，其中一个一次遍历一个节点(慢)，另一个一次遍历两个节点(快)。这样当一个遍历到链表末端后，另一个刚好到一半。  
代码：
```C
struct ListNode* middleNode(struct ListNode* head){
    if(head == NULL || head -> next == NULL){
        return head;
    }
    struct ListNode* slow = head;
    struct ListNode* fast = head;
    while(fast != NULL && fast -> next != NULL){
        slow = slow -> next;
        fast = fast -> next -> next;
    }
    return slow;
}
```
---
# 删除链表的结点
## 题目
给定单向链表的头指针和一个要删除的节点的值，定义一个函数删除该节点。  
返回删除后的链表的头节点。
## 示例
输入: head = [4,5,1,9], val = 5  
输出: [4,1,9]  
解释: 给定你链表中值为 5 的第二个节点，那么在调用了你的函数之后，该链表应变为 4 -> 1 -> 9

## 解法
将选定值所在结点的左节点与右结点连接即可。  
代码：
```C
struct ListNode* deleteNode(struct ListNode* head, int val){
    if(head == NULL){
        return head;
    }
    struct ListNode* newhead = head;
    if(head -> val == val){
        head = head -> next;
        return head;
    }
    while((head -> next -> val != val) && head != NULL){
        head = head -> next;
    }
    struct ListNode* aft = head -> next -> next;
    head -> next = aft;
    return newhead;
}
```