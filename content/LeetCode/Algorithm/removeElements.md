---
title: "移除链表元素"
date: 2021-08-28T14:25:50+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目描述
给你一个链表的头节点 head 和一个整数 val ，请你删除链表中所有满足 Node.val == val 的节点，并返回新的头节点。  

# 解法
## 迭代法
从头到尾遍历链表，发现结点数值复合要求就删除，另写一个删除函数，用来判断头结点、尾结点和中间结点的删除
```java
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        ListNode ptr = head;
        while (ptr != null) {
            if (ptr.val == val) {
                head = delete(head, ptr);
            }
            ptr = ptr.next;
        }
        return head;
    }

    private ListNode delete(ListNode head, ListNode del) {
        if (del == head) {
            head = head.next;
        }
        else if (del.next == null) {
            int pre_index = getIndex(head, del) - 1;
            ListNode ptr = getNode(head, pre_index);
            ptr.next = null;
        }
        else {
            int pre_index = getIndex(head, del) - 1;
            ListNode pre = getNode(head, pre_index);
            ListNode aft = pre.next.next;
            pre.next = aft;
        }
        return head;
    }

    private int getIndex(ListNode head, ListNode search) {
        ListNode ptr = head;
        int index = 0;
        while (ptr != null) {
            if (ptr == search) { return index; }
            ptr = ptr.next;
            index++;
        }
        return -1;
    }

    private ListNode getNode(ListNode head, int index) {
        ListNode ptr = head;
        for (int i = 0; i < index; i++) {
            ptr = ptr.next;
        }
        return ptr;
    }
}
```

## 递归法
递归传参：head.next
递归出口：head == null 时，返回 null
在递归中 head 不断指向自己的 next，因此就是要不断判断 head.val 是否满足要求，如果满足要求，递归函数应返回 head.next，否则，返回 head

```java
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        if (head == null) { return null; }
        head.next = removeElements(head.next, val);
        if (head.val == val) { return head.next; }
        else { return head; }
    }
}
```