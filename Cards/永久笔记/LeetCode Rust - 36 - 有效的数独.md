---
title: LeetCode Rust - 36 - 有效的数独
date created: 2023-08-09
date modified: 2023-08-09
---

> 原题：[36. 有效的数独 - 力扣（LeetCode）](https://leetcode.cn/problems/valid-sudoku/)

## 解一，哈希表模拟

全部遍历一遍，检查当前行、列、块有没有重复的

```rust
pub fn is_valid_sudoku(board: Vec<Vec<char>>) -> bool {
	use std::collections::HashMap;
	let mut row_map = vec![HashMap::<char, bool>::new(); 9];
	let mut col_map = vec![HashMap::<char, bool>::new(); 9];
	let mut block_map = vec![HashMap::<char, bool>::new(); 9];

	fn exist_or_insert(map: &mut Vec<HashMap<char, bool>>, index: usize, value: &char) -> bool {
		if *value == '.' {
			return false;
		}
		if let Some(item) = map.get_mut(index) {
			if item.contains_key(value) {
				return true;
			} else {
				item.insert(*value, true);
				return false;
			}
		}
		false
	}

	for (y, row) in board.iter().enumerate() {
		for (x, value) in row.iter().enumerate() {
			if exist_or_insert(&mut row_map, y, value)
				|| exist_or_insert(&mut col_map, x, value)
				|| exist_or_insert(&mut block_map, x / 3 + y / 3 * 3, value)
			{
				return false;
			}
		}
	}
	return true;
}
```

时间：$O(1)$
空间：$O(1)$

## 解二，数组模拟

跟[[#解一，哈希表模拟]]方法完全一致，只不过使用了数组来替代哈希表，以达到更好的性能

```rust
pub fn is_valid_sudoku(board: Vec<Vec<char>>) -> bool {
	let mut row_map = [[false; 9]; 9];
	let mut col_map = [[false; 9]; 9];
	let mut block_map = [[false; 9]; 9];

	fn exist_or_insert(map: &mut [[bool; 9]; 9], index: usize, value: &char) -> bool {
		if *value == '.' {
			return false;
		}
		if let Some(item) = map.get_mut(index) {
			let i = *value as usize - '1' as usize;
			if item[i] {
				return true;
			} else {
				item[i] = true;
				return false;
			}
		}
		false
	}

	for (y, row) in board.iter().enumerate() {
		for (x, value) in row.iter().enumerate() {
			// check row
			if exist_or_insert(&mut row_map, y, value)
				|| exist_or_insert(&mut col_map, x, value)
				|| exist_or_insert(&mut block_map, x / 3 + y / 3 * 3, value)
			{
				return false;
			}
		}
	}
	return true;
    }
```

时间：$O(1)$
空间：$O(1)$