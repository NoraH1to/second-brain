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

如果需要直接访问其内部的字段/模块成员，需要也用 `pub` 将其设为公开

```rust
mod my_module {
	pub struct Box {
		pub fruit: String;
		pub water: String;
	}
	pub mod child_module {
		pub fn child_fn() {}
	}
}
```

## 构造字段私有的结构体

如果需要构造下面的结构体 `Box`

```rust
mod my_module {
	pub struct Box {
		fruit: String,
		water: String,
	}
}
```

只需要公开实现工厂方法即可

```rust
mod my_module {
    pub struct Box {
        fruit: String,
        water: String,
    }
    impl Box {
        pub fn create_banana_box() -> Box {
            Box {
                fruit: String::from("banana"),
                water: String::from("water"),
            }
        }
    }
}
```

使用

```rust
let banana_box = my_module::Box::create_banana_box();
```

### pub 会公开所有枚举

公开枚举 `IP_ADDR`

```rust
mod my_module {
    pub enum IP_ADDR {
        V4(String),
        V6(String),
    }
}
```

能够直接访问其所有枚举

```rust

```