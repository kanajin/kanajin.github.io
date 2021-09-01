---
title: "比较版本号"
date: 2021-09-01T23:13:30+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目描述
给你两个版本号 version1 和 version2 ，请你比较它们。

版本号由一个或多个修订号组成，各修订号由一个 '.' 连接。每个修订号由 多位数字 组成，可能包含 前导零 。每个版本号至少包含一个字符。修订号从左到右编号，下标从 0 开始，最左边的修订号下标为 0 ，下一个修订号下标为 1 ，以此类推。例如，2.5.33 和 0.1 都是有效的版本号。

比较版本号时，请按从左到右的顺序依次比较它们的修订号。比较修订号时，只需比较 忽略任何前导零后的整数值 。也就是说，修订号 1 和修订号 001 相等 。如果版本号没有指定某个下标处的修订号，则该修订号视为 0 。例如，版本 1.0 小于版本 1.1 ，因为它们下标为 0 的修订号相同，而下标为 1 的修订号分别为 0 和 1 ，0 < 1 。

返回规则如下：

* 如果 version1 > version2 返回 1，
* 如果 version1 < version2 返回 -1，
* 除此之外返回 0。

# 解法
比较每个 '.' 之前的版本号大小，如果比较出大小就返回，比较不出就无视点去比较下一个数字

```java
class Solution {
    public int compareVersion(String version1, String version2) {
        int i = 0;
        int j = 0;
        while (i < version1.length() || j < version2.length()) {
            int x = 0;
            for (; i < version1.length() && version1.charAt(i) != '.'; i++) {
                x += x*10 + (version1.charAt(i) - 48);
            }
            i++;
            int y = 0;
            for (; j < version2.length() && version2.charAt(j) != '.'; j++) {
                y += y*10 + (version2.charAt(j) - 48);
            }
            j++;
            if (x > y) {
                return 1;
            }
            if (x < y) {
                return -1;
            }
        }
        return 0;
    }
}
```