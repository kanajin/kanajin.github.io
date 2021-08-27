---
title: "赎金信"
date: 2021-08-27T16:27:58+08:00
draft: false
categories: ["力扣算法"]
enableMathJax: false
---

# 题目描述
给定一个赎金信 (ransom) 字符串和一个杂志(magazine)字符串，判断第一个字符串 ransom 能不能由第二个字符串 magazines 里面的字符构成。如果可以构成，返回 true ；否则返回 false。

(题目说明：为了不暴露赎金信字迹，要从杂志上搜索各个需要的字母，组成单词来表达意思。杂志字符串中的每个字符只能在赎金信字符串中使用一次。)

提示：

* 你可以假设两个字符串均只含有小写字母。


# 解法
核心思路为比较赎金信中的每个字符出现的个数是否大于杂志中对应字符出现的个数，如果大于，则是 false
## 哈希表法
创造两个哈希表来存储两个字符串中每个字符出现的次数，然后遍历赎金信进行比较
```java
class Solution {
    public boolean canConstruct(String ransomNote, String magazine) {
        HashMap<Character, Integer> map_note = new HashMap<>();
        HashMap<Character, Integer> map_magzine = new HashMap<>();
        for (int i = 0; i < ransomNote.length(); i++) {
            map_note.put(ransomNote.charAt(i), map_note.getOrDefault(ransomNote.charAt(i), 0)+1);
        }
        for (int i = 0; i < magazine.length(); i++) {
            map_magzine.put(magazine.charAt(i), map_magzine.getOrDefault(magazine.charAt(i), 0)+1);
        }
        for (int i = 0; i < ransomNote.length(); i++) {
            if (map_note.get(ransomNote.charAt(i)) > map_magzine.getOrDefault(ransomNote.charAt(i), 0)) { return false; }
        }
        return true;
    }
}
```

## 数组法
由于本题只出现小写字母，因此可以根据此特殊情况进行加速  

创建两个容量为26的数组，数组下标用来表示第几个英文字母  
* char - 'a' 可以表示该英文字母是字母表中第几个

用这种方式来存储每个字符出现了几次，避免了哈希表的Character空间

```java
class Solution {
    public boolean canConstruct(String ransomNote, String magazine) {
        int[] note_arr = new int[26];
        int[] magazine_arr = new int[26];
        for (int i = 0; i < ransomNote.length(); i++) { note_arr[ransomNote.charAt(i) - 'a']++; }
        for (int i = 0; i < magazine.length(); i++) { magazine_arr[magazine.charAt(i) - 'a']++; }
        for (int i = 0; i < 26; i++) {
            if (note_arr[i] - magazine_arr[i] > 0) { return false; }
        }
        return true;
    }
}
```

也可以将数组减少至一个，但似乎没什么卵用
```java
class Solution {
    public boolean canConstruct(String ransomNote, String magazine) {
        int[] arr = new int[26];
        for (int i = 0; i < ransomNote.length(); i++) { arr[ransomNote.charAt(i) - 'a']++; }
        for (int i = 0; i < magazine.length(); i++) { arr[magazine.charAt(i) - 'a']--; }
        for (int i = 0; i < 26; i++) {
            if (arr[i] > 0) { return false; }
        }
        return true;
    }
}
```