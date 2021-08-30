---
title: "用栈实现队列"
date: 2021-08-30T13:02:09+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目描述
请你仅使用两个栈实现先入先出队列。队列应当支持一般队列支持的所有操作（push、pop、peek、empty）：

实现 MyQueue 类：

* void push(int x) 将元素 x 推到队列的末尾
* int pop() 从队列的开头移除并返回元素
* int peek() 返回队列开头的元素
* boolean empty() 如果队列为空，返回 true ；否则，返回 false

 

说明：

* 你只能使用标准的栈操作 —— 也就是只有 push to top, peek/pop from top, size, 和 is empty 操作是合法的。
* 你所使用的语言也许不支持栈。你可以使用 list 或者 deque（双端队列）来模拟一个栈，只要是标准的栈操作即可。

# 解法
两个栈一个in一个out，in用来push，当接收到pop或peek指令时如果out栈是空的则将in栈倒入out  
```java
class MyQueue {

    Stack<Integer> in_stack;
    Stack<Integer> out_stack;

    /** Initialize your data structure here. */
    public MyQueue() {
        in_stack = new Stack<>();
        out_stack = new Stack<>();
    }
    
    /** Push element x to the back of queue. */
    public void push(int x) {
        in_stack.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        if (out_stack.empty()) {    //只有当out栈为空时才需要将in栈的元素倒入，否则不需要
            while (!in_stack.empty()) {
                out_stack.push(in_stack.pop());
            }
        }
        return out_stack.pop();
    }
    
    /** Get the front element. */
    public int peek() {
        if (out_stack.empty()) {
            while (!in_stack.empty()) {
                out_stack.push(in_stack.pop());
            }
        }
        return out_stack.peek();
    }
    
    /** Returns whether the queue is empty. */
    public boolean empty() {
        return in_stack.empty() && out_stack.empty();
    }
}

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue obj = new MyQueue();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.peek();
 * boolean param_4 = obj.empty();
 */

```