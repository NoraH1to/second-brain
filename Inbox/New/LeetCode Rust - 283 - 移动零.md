---
title: LeetCode Rust - 283 - 移动零
date created: 2023-08-08
date modified: 2023-08-08
---

> 原题：[283. 移动零 - 力扣（LeetCode）](https://leetcode.cn/problems/move-zeroes/)

## 解一，双指针+翻转数组

这个解法纯粹是我想太多，根本不用这么麻烦

1. 倒序遍历数组直到不为零，记录下标，作为翻转边界
2. 正序遍历数组查找零
	1. 若直至边界都没有零，则直接返回数组
	2. 若遇到零，以该零为起点，遍历查找下一个零
		1. 如果到边界都没有零，翻转起点到边界的项，返回结果
		2. 如果找到零，翻转起点到零位置 - 1的项，以零位置 - 1为起点开始下一轮循环

```rust
pub fn move_zeroes(nums: &mut Vec<i32>) {
	fn reverse(target: &mut Vec<i32>, mut start: usize, mut end: usize) {
		loop {
			if start >= end {
				break;
			}
			target.swap(start, end);
			start += 1;
			end -= 1;
		}
	}
	let right = nums.len();
	let mut left = 0usize;
	while left < right {
		if nums[left] == 0 {
			let mut i = left;
			while i < right && nums[i] == 0 {
				i += 1;
			}
			if i == right { return; };
			let c = i - left;
			while i < right && nums[i] != 0 {
				i += 1;
			}
			i -= 1;
			reverse(nums, left + c, i);
			reverse(nums, left, i);
			left = i - c + 1;
		} else {
			left += 1;
		}
	}
}
```

时间：$O(n^2)$
空间：$O(n)$

## 解二，双指针

1. 一个右指针，每次循环自增
2. 一个左指针，当右指针不为零时，交换左右指针的值，左指针自增

```rust

```