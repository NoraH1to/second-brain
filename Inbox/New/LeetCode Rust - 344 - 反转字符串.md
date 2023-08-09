---
title: LeetCode Rust - 344 - 反转字符串
date created: 2023-08-09
date modified: 2023-08-09
---

> 原题：[344. 反转字符串 - 力扣（LeetCode）](https://leetcode.cn/problems/reverse-string/)

## 解一，双指针翻转

```rust
pub fn reverse_string(s: &mut Vec<char>) {
	let mut left = 0usize;
	let mut right = s.len() - 1;
	while left < right {
		s.swap(left, right);
		left += 1;
		right -= 1;
	}
}
```

时间：$O(n)$
空间：$O(1)$