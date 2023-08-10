---
title: LeetCode Rust - 242 - 有效的字母异位词
date created: 2023-08-10
date modified: 2023-08-10
---

> 原题：[242. 有效的字母异位词 - 力扣（LeetCode）](https://leetcode.cn/problems/valid-anagram/)

## 解一，哈希表

用两个哈希表分别记录两个字符串中各字符出现的次数，最后遍历较长的字符串，对比两个哈希表中字符出现的次数

```rust
pub fn is_anagram(s: String, t: String) -> bool {
	use std::collections::HashMap;
	let mut s_map = HashMap::<char, u32>::new();
	let mut t_map = HashMap::<char, u32>::new();
	
	for mut item in [(s, &mut s_map), (t, &mut t_map)] {
		for char in item.0.chars() {
			let count = item.1.entry(char).or_insert(0);
			*count += 1;
		}
	}

	let mut maps = if s_map.len() > t_map.len() {
		(s_map, t_map)
	} else {
		(t_map, s_map)
	};

	for (char, count) in maps.0 {
		if *(maps.1.entry(char).or_insert(0)) != count {
			return false;
		}
	}

	return true;
}
```

时间：$O(2n + m)$
空间：$O(n + m)$

## 解二，哈希表（优化版）

[[#解一，哈希表]] 中使用了两个哈希表，空间和时间上都还能再优化

这边使用一个数组来替代这两个哈希表

- 如果两**字符串长度不等**，返回 `false`
- 题目中只有小写字母，声明长度为 `26` 的数组，`hash_arr`
- 遍历第一个字符串，对每个字符 `char`
	- `hash_arr[char - 'a']` **自增**
- 遍历第二个字符串，对每个字符 `char`
	- 如果 `hash_arr[char - 'a']` **等于零**，返回 `false`
	- 否则 `hash_arr[char - 'a']` **自减**

```rust
pub fn is_anagram(s: String, t: String) -> bool {
	if s.len() != t.len() {
		return false;
	}
	let mut list_arr = [0; 26];
	for char in s.chars() {
		list_arr[char as usize - 'a' as usize] += 1;
	}
	for char in t.chars() {
		let mut count = list_arr.get_mut(char as usize - 'a' as usize).unwrap();
		if *count == 0 {
			return false;
		} else {
			*count -= 1;
		}
	}
	return true;
}
```

时间：$O(n)$
空间：$O(1) = O(S)$

> $n$ 为单个字符串的长度
> $S$ 为字符集大小，题目中为 $S = 26$

## 解三，排序

将两个字符串排序，然后依次比较

```rust
```