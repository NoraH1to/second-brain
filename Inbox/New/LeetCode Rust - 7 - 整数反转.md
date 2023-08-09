---
title: LeetCode Rust - 7 - 整数反转
date created: 2023-08-09
date modified: 2023-08-09
---

> 原题：[7. 整数反转 - 力扣（LeetCode）](https://leetcode.cn/problems/reverse-integer/)

## 解一，循环取模

- 循环取 `x % 10` 的模 `k` 同时削去 `x` 一位低位 
- 将 `k` 与上次结果 `res` 乘以 `10` 后相加得到新的 `res`
	- 如果不是最后一位，则检查溢出，当 `res * 10 / 10` 不等于 `res` 时返回零
- 直到 `k = 0` 时退出循环，返回 `res`

```rust
pub fn reverse(mut x: i32) -> i32 {
	let mut res = 0i32;
	while x != 0 {
		let k = x % 10;
		res = res * 10 + k;
		if (x.abs() >= 10) && (res * 10 / 10 != res) {
			return 0;
		}
		x /= 10;
	}
	return res;
}
```

时间：$O(n)$

> $n$ 为整数位数

空间：$O(1)$