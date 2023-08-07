---
title: LeetCode Rust - 217. 存在重复元素
date created: 2023-08-06
date modified: 2023-08-06
---

> 原题：[217. 存在重复元素 - 力扣（LeetCode）](https://leetcode.cn/problems/contains-duplicate/)

## 解一，哈希表

开一个哈希表记录是否已经存在某数

```rust
impl Solution {
    pub fn contains_duplicate(nums: Vec<i32>) -> bool {
        use std::collections::HashMap;
        let mut map: HashMap<i32, bool> = HashMap::new();
        for v in nums {
            match map.get(&v) {
                Some(_) => return true,
                None => { map.insert(v, true); },
            }
        }
        false
    }
}
```

时间：$O(n)$
空间：$O(n)$