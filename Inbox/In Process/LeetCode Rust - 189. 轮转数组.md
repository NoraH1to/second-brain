---
title: LeetCode Rust - 189. 轮转数组
date created: 2023-08-05
date modified: 2023-08-05
---

> 原题：[189. 轮转数组 - 力扣（LeetCode）](https://leetcode.cn/problems/rotate-array/description/)

## 解一，遍历推入新数组

按照：_给定的下标处到数组末尾、从0到指定下标前_ 的顺序将元素推入到新数组

```rust
impl Solution {
    pub fn rotate(nums: &mut Vec<i32>, k: i32) {
        if k == 0 { return }
        let len = nums.len();
        let _k = len - k as usize % len;
        let mut res: Vec<i32> = vec![];
        for i in _k..len {
            res.push(nums[i]);
        }
        for i in 0.._k {
            res.push(nums[i]);
        }
        *nums = res
    }
}
```

## 解二，原地排序

#TODO 