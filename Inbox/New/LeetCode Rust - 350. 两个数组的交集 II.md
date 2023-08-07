
> 原题：[350. 两个数组的交集 II - 力扣（LeetCode）](https://leetcode.cn/problems/intersection-of-two-arrays-ii/)

## 解一，哈希表

利用哈希表记录数字出现次数，求的是交集，为了节约空间先遍历较短的数组

- 遍历第一个数组时对哈希表计数自增
遍历第二个数组时，对哈希表存在的数字且计数大于零的，计数自减并将数字入结果栈，如果

```rust

```