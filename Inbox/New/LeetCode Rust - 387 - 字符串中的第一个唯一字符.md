---
title: LeetCode Rust - 387 - 字符串中的第一个唯一字符
date created: 2023-08-10
date modified: 2023-08-10
---

> 原题：[387. 字符串中的第一个唯一字符 - 力扣（LeetCode）](https://leetcode.cn/problems/first-unique-character-in-a-string/)

## 解一，哈希表

遍历两次，第一次哈希表字符计数，第二次找到第一个只有一次计数的下标

> 字符串中只有小写字母，用数组替代哈希表可以获得更好的性能（减少哈希计算开销）

```rust
pub fn first_uniq_char(s: String) -> i32 {
	use std::collections::HashMap;
	let mut map = HashMap::<char, i32>::new();
	for char in s.chars() {
		let count = map.entry(char).or_insert(0);
		*count += 1;
	}
	for (i, char) in s.chars().into_iter().enumerate() {
		if *(map.get(&char).unwrap()) == 1 {
			return i as i32;
		}
	}
	return -1;
}
```

时间：$O(2n) = O(n)$

> $n$ 为字符串长度

空间：$O(1)$

## 解二，队列

