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

整体借鉴了快排的思维，把所有非零的值按顺序摆放到最前面

1. 声明左右指针 `left` 和 `right`
2. 写一个循环，每次循环 `right` 自增，检查当 `right` 指向的值是否为零
	- 若为零，则继续循环
	- 否则交换 `left` `right` 位置的值，此时 `left` 的位置已经安放了正确的值，`left` 自增指向下一项

```rust
pub fn move_zeroes(nums: &mut Vec<i32>) {
	let mut left = 0usize;
	let mut right = 0usize;
	while right < nums.len() {
		if nums[right] != 0 {
			nums.swap(left, right);
			left += 1;
		}
		right += 1;
	}
}
```

时间：$O(n)$
空间：$O(1)$