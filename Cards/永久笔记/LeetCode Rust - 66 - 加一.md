---
title: LeetCode Rust - 66 - 加一
date created: 2023-08-08
date modified: 2023-08-08
---

> 原题：[66. 加一 - 力扣（LeetCode）](https://leetcode.cn/problems/plus-one/)

## 解一，倒序遍历

```rust
pub fn plus_one(mut digits: Vec<i32>) -> Vec<i32> {
	let mut i = digits.len() - 1;
	loop {
		digits[i] += 1;
		if digits[i] > 9 {
			if i == 0 {
				digits[i] = 1;
				digits.push(0);
			} else {
				digits[i] = 0;
			}
		} else { break; }
		if i == 0 { break; } else { i -= 1; }
	}
	digits
}
```

时间：$O(n)$
空间：$O(1)$