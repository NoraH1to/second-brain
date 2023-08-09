
> 原题：[7. 整数反转 - 力扣（LeetCode）](https://leetcode.cn/problems/reverse-integer/)

## 解一，循环取模

- 循环取 `x % 10` 的模 `k` 同时削去 `x` 一位低位 
- 将 `k` 与上次结果 `res` 乘以 `10` 后相加得到新的 `res`
	- 检查溢出，如果 `res / 10 * 10` 不等于 `res`，返回零
- 直到 `k = 0` 时退出循环，返回 `res`

```rust

```