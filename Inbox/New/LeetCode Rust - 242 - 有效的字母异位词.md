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

- 如果两zi