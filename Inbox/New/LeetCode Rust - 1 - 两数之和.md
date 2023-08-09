
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

## 解二，头尾双指针遍历

非常经典的解法

1. 从低到高排序数组
2. 头尾各设一个指针 `left`, `right`
	1. 若 `nums[left] + nums[rigt]` **小于** `target`，此时需要**更大**的和， `left` 自增
	2. 若**大于** `target`，此时需要**更小**的和， `right` 自减
	3. **等于**则直接返回结果

```rust

```