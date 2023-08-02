
## 基础

### pub 只能公开结构体和模块本身的引用

假设下面模块 `my_module`

```rust
mod my_module {
	// 公开了 Box
	pub struct Box {
		fuit: String;
		water: String;
	}
}
```

如果引用