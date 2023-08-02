---
title: Rust NoteBook
date created: 2023-08-02
date modified: 2023-08-02
---

## 基础

### pub 只能公开结构体和模块本身

假设下面模块 `my_module`

```rust
mod my_module {
	pub struct Box {
		fruit: String;
		water: String;
	}
	pub mod child_module {
		fn child_fn() {}
	}
}
```

下面的代码都会报错

```rust
let my_box = my_module::Box {
	fruit: String::from("banana"), // field `fruit` of struct `my_module::Box` is private
	water: String::from("water"), // field `water` of struct `my_module::Box` is private
};
```

```rust
my_module::child_module::child_fn(); // function `child_fn` is private
```

## 构造字段私有的结构体

```rust
mod my_module {
	pub struct Box {
		fruit: String,
		water: String,
	}
}
```