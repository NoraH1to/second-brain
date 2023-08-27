---
title: LeetCode Rust - 8 - 字符串转换整数 (atoi)
date created: 2023-08-17
date modified: 2023-08-17
---

> 原题：[8. 字符串转换整数 (atoi) - 力扣（LeetCode）](https://leetcode.cn/problems/string-to-integer-atoi/)

## 解一，遍历模拟

- 遍历字符，直到遇见符号或数字，如果遇到非符号和字符串，返回零
- 如果找不到符号或者数字，返回零
- 判断是否为符号，如果是，记录符号 `sign`，指针前进一步，如果进步后越界，则返回零
- 循环遍历剩余字符直到不是数字，对每个数字字符 `char` 和结果 `res`：
	- `res *= 10`
	- `res += char - 48`
	> 这里使用 rust 内置的标准函数，保证整形不会溢出
	> `res` 以有符号 64 位存储
- 根据 `sign` 判断是否溢出，溢出返回其最大/最小值，否则返回其本身

```rust
pub fn my_atoi(s: String) -> i32 {
	fn is_number(char: u8) -> bool {
		char >= b'0' && char <= b'9'
	}
	fn is_sign(char: u8) -> bool {
		char == b'+' || char == b'-'
	}

	let mut i = 0usize;
	let len = s.len();
	let mut res = 0i64;
	let bytes = s.as_bytes();

	while i < len {
		if bytes[i] == b' ' {
			i += 1;
		} else if is_number(bytes[i]) || is_sign(bytes[i]) {
			break;
		} else {
			return res as i32;
		}
	}
	if i >= len {
		return res as i32;
	};

	let mut sign = '+';
	if is_sign(bytes[i]) {
		if i + 1 >= len {
			return res as i32;
		} else {
			sign = bytes[i] as char;
			i += 1
		}
	}

	while i < len && is_number(bytes[i]) {
		res = res.saturating_mul(10);
		res = res.saturating_add((bytes[i] - 48u8) as i64);
		i += 1;
	}
	
	return if sign == '+' {
		(i32::MAX as i64).min(res) as i32
	} else {
		res = res.saturating_mul(-1);
		(i32::MIN as i64).max(res) as i32
	};
}
```