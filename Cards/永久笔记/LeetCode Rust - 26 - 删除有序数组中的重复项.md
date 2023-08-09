---
title: LeetCode Rust - 26 - 删除有序数组中的重复项
date created: 2023-08-04
date modified: 2023-08-04
---

> 原题：[26. 删除有序数组中的重复项 - 力扣（LeetCode）](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/)

## 解一，原生方法

题目中提到了数组是排好序的，那么首先看看有没有原生的方法能做到去重，查阅文档后找到了 [Vec in std::vec - Rust (rust-lang.org)](https://doc.rust-lang.org/std/vec/struct.Vec.html#method.dedup)，该方法能去除 vector 中**连续重复**的项

```rust
pub fn remove_duplicates(nums: &mut Vec<i32>) -> i32 {
	nums.dedup();
	nums.len() as i32
}
```

## 解二，从后向前遍历删除

从后往前遍历，比较当前项和上一个项，如果相同则删除上一个项，遍历边界为 `i < 0`

```rust
pub fn remove_duplicates(nums: &mut Vec<i32>) -> i32 {
	let mut i  = (nums.len() - 1) as isize;
	loop {
		if i < 0 {
			break;
		}
		let pre = match nums.get((i - 1) as usize) {
			Some(n) => n,
			None => break,
		};
		let cur = nums[i as usize];
		if cur == *pre {
			nums.remove((i - 1) as usize);
		}
		i -= 1;
	};
	nums.len() as i32
}
```

时间：$O(n)$
空间：$O(1)$

> [!FAQ] 为什么不是从前向后遍历？
> 
> 题目要求**原地删除**，如果从前向后遍历，每次删除时，由于数组长度发生变化，需要更新循环边界，并且当前项的指针指向也需要写代码维护，很麻烦且会降低性能

这里踩了个坑，[[Rust NoteBook#整形转换的溢出和占用问题]]