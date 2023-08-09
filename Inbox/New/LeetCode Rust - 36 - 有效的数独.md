
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