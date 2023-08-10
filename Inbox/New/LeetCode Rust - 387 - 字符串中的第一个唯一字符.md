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

遍历字符串，对于每个字符 `char`
- 如果哈希表中不存在该字符
	- 在哈希表中记录该字符为 `true`
	- 将记录了字符和下标的元组 `(char, index)` 推入队尾
- 如果哈希表中存在该字符
	- 在哈希表中记录该字符为 `false`
	- 循环检查队列头部项
		- 如果在哈希表中标记为 `false`，取出丢弃
		- 如果在哈希表中标记为 `true` 或**队列为空**，结束循环
- 根据队列返回结果
	- 如果**队列为空**，则不存在只出现一次的字符，返回 `-1`
	- 否则**队列中的头部项**就是结果，返回其下标

```rust
pub fn first_uniq_char(s: String) -> i32 {
	use std::collections::HashMap;
	use std::collections::VecDeque;
	let mut map = HashMap::<char, bool>::new();
	let mut list = VecDeque::<(char, usize)>::new();
	for (i, char) in s.chars().into_iter().enumerate() {
		match map.get(&char) {
			Some(_) => {
				map.insert(char, false);
				while !list.is_empty() && !map.get(&(list.front().unwrap().0)).unwrap() {
					list.pop_front();
				}
			}
			None => {
				map.insert(char, true);
				list.push_back((char, i));
			}
		}
	}

	return if list.is_empty() { -1 } else { list.front().unwrap().1 as i32 };
}
```

时间：$O(n) + O(字符类型 * 2) = O(n)$

> $n$ 为字符串长度
> $$

空间：$O(1)$