---
title: LeetCode 3.Longest Substring Without Repeating Characters
date: 2020-09-06 21:37:17
tags: leetcode
---

求给定字符串的不含重复字符的最长子串的长度

<!-- more -->

```Java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int[] count = new int[150];
        int l = 0, r = 0, ans = 0;
        while (r < s.length()) {
            count[s.charAt(r)]++;
            if (count[s.charAt(r)] > 1) {
                while (count[s.charAt(r)] > 1) {
                    count[s.charAt(l)]--;
                    l++;
                }
            }
            r++;
            ans = Math.max(ans, r - l);
        }
        return ans;
    }
}
```
