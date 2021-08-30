---
title: "删除链表重复元素"
date: 2021-08-30T00:21:58+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目描述
存在一个按升序排列的链表，给你这个链表的头节点 head ，请你删除所有重复的元素，使每个元素 只出现一次 。

返回同样按升序排列的结果链表。

# 解法
递归，当head or head.next == null 时，递归出口

如果当前值和递归返回值相同，则当前值next为递归返回值next

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {

        if (head == null || head.next == null) {
            return head;
        }

        ListNode ptr = deleteDuplicates(head.next);

        if (ptr != null && head.val == ptr.val) {
            head.next = ptr.next;
            return head;
        }
        else {
            return head;
        }

    }
}
```