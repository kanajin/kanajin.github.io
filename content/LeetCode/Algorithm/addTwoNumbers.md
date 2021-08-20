---
title: "两数相加"
date: 2021-08-20T21:38:20+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目
给你两个 非空 的链表，表示两个非负的整数。它们每位数字都是按照 逆序 的方式存储的，并且每个节点只能存储 一位 数字。

请你将两个数相加，并以相同形式返回一个表示和的链表。

你可以假设除了数字 0 之外，这两个数都不会以 0 开头。
# 解法
逐位相加，如果加和的值大于10，则记录进一，下一次的逐位相加应加上该进位，如果最高一位溢出，则再新建一个尾结点，值为1
```Java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    ListNode head = null;
    ListNode tail = null;
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        int overflow = 0;   //记录进位
        while(l1 != null || l2 != null){
            if(l1 != null && l2 != null){
                ListNode temp = new ListNode((l1.val + l2.val + overflow)%10, null);
                addAtTail(temp);
                if(l1.val + l2.val + overflow >= 10){
                    overflow = 1;   //溢出，进位
                }
                else{
                    overflow = 0;   //若不再溢出，复原进位
                }
                l1 = l1.next;
                l2 = l2.next;
            }
            else{
                if(l1 == null){
                    ListNode temp = new ListNode((l2.val + overflow)%10, null);
                    addAtTail(temp);
                    if(l2.val + overflow >= 10){
                        overflow = 1;
                    }
                    else{
                        overflow = 0;
                    }
                    l2 = l2.next;
                }
                else{
                    ListNode temp = new ListNode((l1.val + overflow)%10, null);
                    addAtTail(temp);
                    if(l1.val + overflow >= 10){
                        overflow = 1;
                    }
                    else{
                        overflow = 0;
                    }
                    l1 = l1.next;
                }
            }
        }
        if(overflow == 1){
            ListNode temp = new ListNode(1, null);
            addAtTail(temp);
        }
        return head;
    }
    //尾插
    private void addAtTail(ListNode ele){
        if(head == null && tail == null){
            head = ele;
            tail = ele;
        }
        else{
            tail.next = ele;
            tail = tail.next;
        }
    }
}
```