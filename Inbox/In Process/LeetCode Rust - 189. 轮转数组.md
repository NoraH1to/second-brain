---
title: LeetCode Rust - 189. 轮转数组
date created: 2023-08-05
date modified: 2023-08-06
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

时间 $O(n)$ 
空间 $O(n)$

## 解二，数组翻转

假设有数组 `[1, 2, 3, 4, 5, 6, 7]` 且 `k = 2`：

1. 先把数组整个翻转
	`[7, 6, 5, 4, 3, 2, 1]`
2. 然后把前 `k` 个项翻转
	`[6, 7, 5, 4, 3, 2, 1]`
3. 最后把剩余的项翻转，完成
	`[6, 7, 1, 2, 3, 4, 5]`

```rust

```

时间：$O(2n) = O(n)$
空间 $O(1)$

#TODO 