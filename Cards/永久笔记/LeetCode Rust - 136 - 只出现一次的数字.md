---
title: LeetCode Rust - 136 - 只出现一次的数字
date created: 2023-08-07
date modified: 2023-08-07
---

> 原题：[136. 只出现一次的数字 - 力扣（LeetCode）](https://leetcode.cn/problems/single-number/)

## 解一，位运算

题目限制了时间空间复杂度，因此不能用哈希表或者排序扫描等方法解决

并且题目提示：

> 除了某个元素只出现一次以外，其余每个元素均**出现两次**

此时我们可以想到异或，异或有下面三个特点：

1. 当一个数与其本身异或时，结果为零：$a\oplus a = 0$
2. 当一个数与零异或事，结尾为其本身：$a\oplus 0 = a$
3. 异或运算满足**交换律**和**结合律**

因此可以对所有数进行异或运算，重复出现两次的元素全部抵消为零，假设孤立的元素为 $b$ ，则最后的结果为：$b\oplus 0 = b$

```rust
impl Solution {
    pub fn single_number(nums: Vec<i32>) -> i32 {
        nums.iter().fold(0, |cur, x| cur ^ x)
    }
}
```