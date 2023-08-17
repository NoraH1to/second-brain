---
title: LeetCode Rust - 125 - 验证回文串
date created: 2023-08-17
date modified: 2023-08-17
---

> 原题：[125. 验证回文串 - 力扣（LeetCode）](https://leetcode.cn/problems/valid-palindrome/)

## 解一，双指针

首尾添加双指针，找到两边第一个字母进行比较，相同则继续循环，否则返回 `false`

```rust
pub fn is_palindrome(s: String) -> bool {
	const A: i32 = 'A' as i32;
	const Z: i32 = 'Z' as i32;
	const a: i32 = 'a' as i32;
	const z: i32 = 'z' as i32;
	const zero: i32 = '0' as i32;
	const nine: i32 = '9' as i32;

	fn check_char(code: i32) -> bool {
		(code >= A && code <= Z) || (code >= a && code <= z) || (code >= zero && code <= nine)
	}

	let mut i = 0usize;
	let mut j = s.len() - 1;
	let chars: Vec<char> = s.chars().collect();
	while i < j {
		if !check_char(chars[i] as i32) {
			i += 1;
			continue;
		}
		if !check_char(chars[j] as i32) {
			j -= 1;
			continue;
		}
		if (!chars[i].to_lowercase().eq(chars[j].to_lowercase())) {
			return false;
		}
		i += 1;
		j -= 1;
	}
	return true;
}
```

时间：$O(n)$

> $n$ 为字符串长度

空间：$O(1)$