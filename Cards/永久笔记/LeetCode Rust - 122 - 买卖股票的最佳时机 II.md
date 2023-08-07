---
title: LeetCode Rust - 122 - 买卖股票的最佳时机 II
date created: 2023-08-05
date modified: 2023-08-05
---

> 原题：[122. 买卖股票的最佳时机 II - 力扣（LeetCode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/)

## 解一，贪心

能赚就进行交易

```rust
impl Solution {
    pub fn max_profit(prices: Vec<i32>) -> i32 {
        let mut cur: i32 = prices[0];
        let mut res: i32 = 0;
        for p in &prices[1..] {
            if *p > cur {
                res += *p - cur;
            }
            cur = *p;
        }
        res
    }
}
```

## 解二，更简洁的贪心

稍微观察下可以发现，只需比较当天与昨天进行交易是否能赚钱即可，去除[[#解一，贪心]]中的中间变量 `cur`

```rust
impl Solution {
    pub fn max_profit(prices: Vec<i32>) -> i32 {
        let mut res: i32 = 0;
        for i in 1..prices.len() {
            res += 0.max(prices[i] - prices[i - 1]); // 收益为负则不交易（res += 0）
        }
        res
    }
}
```