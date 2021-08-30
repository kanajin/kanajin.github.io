---
title: "反转链表"
date: 2021-08-30T00:24:21+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目描述
给你单链表的头节点 head ，请你反转链表，并返回反转后的链表。 

# 解法
## 递归法：
递归到链表尾部(不是null)，然后改写他的指向

```java
class Solution {

    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode ptr = reverseList(head.next);
        head.next.next = head;  //此段为改写指向，熟记
        head.next = null;
        return ptr;
    }

}
```

## 数组法
新建一个数组来存储链表所有元素，然后从尾到头添加新链表
```java
class Solution {

    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ArrayList<Integer> arr = new ArrayList<>();
        while (head != null) {
            arr.add(head.val);
            head = head.next;
        }
        ListNode new_head = new ListNode(arr.get(arr.size()-1), null);
        ListNode ptr = new_head;
        for (int i = arr.size()-2; i >= 0; i--) {
            ListNode add = new ListNode(arr.get(i), null);
            ptr.next = add;
            ptr = ptr.next;
        }
        return new_head;
    }

}
```