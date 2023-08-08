---
title: LeetCode Rust - 283 - 移动零
date created: 2023-08-08
date modified: 2023-08-08
---

> 原题：[283. 移动零 - 力扣（LeetCode）](https://leetcode.cn/problems/move-zeroes/)

## 解一，双指针+翻转数组

1. 倒序遍历数组直到不为零，记录最后一个零的下标，作为翻转边界
2. 正序遍历数组直至边界