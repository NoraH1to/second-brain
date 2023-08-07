---
title: LeetCode Rust - 350. 两个数组的交集 II
date created: 2023-08-07
date modified: 2023-08-07
---

> 原题：[350. 两个数组的交集 II - 力扣（LeetCode）](https://leetcode.cn/problems/intersection-of-two-arrays-ii/)

## 解一，哈希表

利用哈希表记录数字出现次数，求的是交集，为了节约空间先遍历较短的数组

- 遍历第一个数组时对哈希表计数自增
- 遍历第二个数组时，将哈希表存在的数字且计数大于零的，计数自减并将数字入结果栈

```rust
impl Solution {
    pub fn intersect(nums1: Vec<i32>, nums2: Vec<i32>) -> Vec<i32> {
        use std::collections::HashMap;
        let mut res: Vec<i32> = vec![];
        let mut map: HashMap<i32, usize> = HashMap::new();
        let min = if nums1.len() < nums2.len() { &nums1 } else { &nums2 };
        let max = if nums1.len() < nums2.len() { &nums2 } else { &nums1 };
        for num in min {
            match map.get(num) {
                None => { map.insert(*num, 1usize); },
                Some(count) => { map.insert(*num, count + 1); },
            }
        }
        for num in max {
            match map.get(num) {
                None => {},
                Some(count) => {
                    if (*count > 0) {
                        map.insert(*num, count - 1);
                        res.push(*num);
                    }
                },
            }
        }
        res
    }
}
```

时间：$O(m+n)$
空间：$O(min(m,n))$

## 解二，排序+双指针扫描

从两数组首个项开始遍历，比较值：

- 等于入结果栈
- 哪边小与另一侧，小的那侧移动指针

直到某一侧数组遍历完成

```rust

```