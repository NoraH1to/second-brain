---
title: LeetCode Rust - 48 - 旋转图像
date created: 2023-08-09
date modified: 2023-08-09
---

> 原题：[48. 旋转图像 - 力扣（LeetCode）](https://leetcode.cn/problems/rotate-image/)

## 解一，模拟旋转

暴力模拟，懒得写

## 解二，翻转两次

1. 先沿着**左下到右上的对角线**左右翻转
	-  从左下角的元素开始，将该元素**上方**和**右侧**的项依次交换位置
2. 再验证**水平中线**上下翻转

```rust
pub fn rotate(matrix: &mut Vec<Vec<i32>>) {
	let max_index = matrix.len() - 1;
	let mut x = 0usize;
	let mut y = max_index;
	
	while x < max_index && y > 0 {
		let mut i = x + 1;
		let mut j = y - 1;
		while i <= max_index && j >= 0 {
			let tmp = matrix[y][i];
			matrix[y][i] = matrix[j][x];
			matrix[j][x] = tmp;
			if j == 0 { break; } // usize 类型为非负数，再自减会报错，手动 break
			i += 1;
			j -= 1;
		}
		x += 1;
		y -= 1;
	}

	for i in 0..max_index + 1 {
		let mut top = 0usize;
		let mut bottom = max_index;
		while top < bottom {
			let tmp = matrix[top][i];
			matrix[top][i] = matrix[bottom][i];
			matrix[bottom][i] = tmp;
			top += 1;
			bottom -= 1;
		}
	}
}
```

时间：$O(n^2)$

> 这里的 $n$ 是边长，每次翻转都要访问一遍所有元素，总共访问两次，宽松点就是 $n^2$，严格点是 $2n^2$

空间：$O(1)$