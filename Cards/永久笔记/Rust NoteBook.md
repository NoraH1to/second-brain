---
title: Rust NoteBook
date created: 2023-08-02
date modified: 2023-08-06
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

### 构造字段私有的结构体

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
let ip_v4 = my_module::IP_ADDR::V4(String::from("127.0.0.1"));
let ip_v6 = my_module::IP_ADDR::V6(String::from("::1"));
```

### use 关键字只在其作用域内有效

在根模块 `use` 创建 `IP_ADDR` 的短链

```rust
mod my_module {
    pub enum IP_ADDR {
        V4(String),
        V6(String),
    }
}

use my_module::IP_ADDR;
```

在根作用域中使用没问题

```rust
let ip_v4 = IP_ADDR::V4(String::from("127.0.0.1"));
```

不允许在其它作用域用

```rust
mod another_module {
    pub fn create_ip_v4(v4: String) {
        IP_ADDR::V4(v4) // failed to resolve: use of undeclared type `IP_ADDR`
    }
}
```

### 整形转换的溢出和占用问题

从高位转低位时，溢出的位数会直接被截去，例如下面的无符号16位转8位：

```rust
let num = 256u16; // 256 100_000_000
let newNum = num as u8; // 0 00_000_000
```

同理，无符号转有符号时，符号位也会占用一个位置：

```rust
let a = 255u8 // 255 11_111_111
let b = a as i8; // -1 11_111_111
```

上面 `b` 的最高位不再表示数值本身，而是符号

### 函数可变传参

对于可变引用，可以这样定义：

```rust
fn do_something(str: &mut String) {}
```

对于可变值，`mut` 位置会不太一样：

```rust
fn do_something(mut count: i32) {}
```