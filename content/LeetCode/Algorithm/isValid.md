---
title: "有效的括号"
date: 2021-08-30T12:45:45+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目描述
给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。

有效字符串需满足：

1. 左括号必须用相同类型的右括号闭合。
2. 左括号必须以正确的顺序闭合。

> 正确的顺序示例：  
> "{[]}"  ，  "()[]{}"  
> 错误的顺序示例：  
> "([)]"  ，  "(]"

# 解法
根据正确顺序原则，上括号先入栈，匹配到下括号时，弹出栈顶，查看是否匹配，如果不匹配则返回false  
如果最后栈为空，说明完全匹配

```java
class Solution {
    public boolean isValid(String s) {
        if (s.length() % 2 != 0) {
            return false;
        }
        Stack<Character> st = new Stack<>();
        char[] s_arr = s.toCharArray();
        for (int i = 0; i < s.length(); i++) {
            if (isFrontBracket(s_arr[i])) {
                st.push(s_arr[i]);
            }
            else {
                if (st.empty()) { return false; }
                if (s_arr[i] != getEndBracket(st.pop())) {
                    return false;
                }
            }
        }
        if (st.empty()) { return true; }
        else { return false; }
    }

    private boolean isFrontBracket(char c) {
        if (c == '(' || c == '[' || c == '{') {
            return true;
        }
        else {
            return false;
        }
    }

    private char getEndBracket(char c) {
        if (c == '(') { return ')'; }
        if (c == '[') { return ']'; }
        if (c == '{') { return '}'; }
        return 0;
    }
}
```