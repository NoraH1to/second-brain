
> 原题：[1. 两数之和 - 力扣（LeetCode）](https://leetcode.cn/problems/two-sum/)

非常经典的入门题
## 解一，双指针枚举

没啥好说的，所有组合尝试一次直到找到答案

```rust
pub fn two_sum(nums: Vec<i32>, target: i32) -> Vec<i32> {
	for (i, l) in nums.iter().enumerate() {
		for (j, r) in nums[(i + 1)..].iter().enumerate() {
			if l + r == target { return vec![i as i32, (j + i + 1) as i32]; }
		}
	}
	return vec![]; // 编译器不知道必定会返回，确保编译通过
}
```

时间：$O(n^2)$
空间：$O(1)$

## 解二，哈希表

非常经典的解法，遍历每个元素，对每个元素 `v`

1. 新建哈希表，值指向下标 `value => index` 的结构
1. 检查哈希表中是否存在 `target - v` 的下标
	1. 如果存在，直接返回结果
	2. 不存在则在表中记录当前值的下标

```rust

```