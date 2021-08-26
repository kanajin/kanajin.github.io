---
title: "救生艇"
date: 2021-08-26T10:03:57+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目描述
第 i 个人的体重为 people[i]，每艘船可以承载的最大重量为 limit。

每艘船最多可同时载两人，但条件是这些人的重量之和最多为 limit。

返回载到每一个人所需的最小船数。(保证每个人都能被船载)。

# 解法
贪心算法：
先对数组进行排序  

考察最轻的人，如果他可以和最重的人同乘一艘船，那么从数组中把他们扣掉，船数+1  

如果他不可以和最重的人同乘一艘船，那么没有人可以和最重的人同乘，因此将最重的人扣除，船数+1

```java
class Solution {
    public int numRescueBoats(int[] people, int limit) {
        Arrays.sort(people);
        int lightest = 0;
        int heavyest = people.length - 1;
        int times = 0;
        while (lightest <= heavyest) {
            if (people[lightest] + people[heavyest] <= limit) {
                lightest++;
            }
            heavyest--;
            times++;
        }
        return times;
    }
}
```