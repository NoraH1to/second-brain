---
title: LeetCode Rust 26. 删除有序数组中的重复项
date created: 2023-08-04
date modified: 2023-08-04
---

> 原题：[26. 删除有序数组中的重复项 - 力扣（LeetCode）](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/)

## 解一，原生方法

题目中提到了数组是排好序的，那么首先看看有没有原生的方法能做到去重，查阅文档后找到了 [Vec in std::vec - Rust (rust-lang.org)](https://doc.rust-lang.org/std/vec/struct.Vec.html#method.dedup)，该方法能去除 Vector 中**连续重复**的项

```rust
impl Solution {
    pub fn remove_duplicates(nums: &mut Vec<i32>) -> i32 {
        nums.dedup();
        nums.len() as i32
    }
}
```

## 解二

