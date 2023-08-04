---
title: cargo 配置
date created: 2023-08-04
date modified: 2023-08-04
---

## 配置文件路径

假设 cargo 在目录 `/projects/foo/bar/baz` 中执行，它会按照如下顺序寻找 `config.toml` 配置文件：

- `/projects/foo/bar/baz/.cargo/config.toml`
- `/projects/foo/bar/.cargo/config.toml`
- `/projects/foo/.cargo/config.toml`
- `/projects/.cargo/config.toml`
- `/.cargo/config.toml`
- 在不同系统中有不同默认值的 `$CARGO_HOME/config.toml`:
    - Windows: `%USERPROFILE%\.cargo\config.toml`
    - Unix: `$HOME/.cargo/config.toml`

简单来说就是**就近原则**

## 配置命令的默认参数

执行 `cargo new` 会默认初始化 Git 仓库，如果我们不想初始化任何版本管理工具，需要加上参数 `cargo new --vcs=none`

如果需要将 `vcs` 默认值改为 `none` ，我们需要添加如下配置：

```toml
[cargo-new]
vcs = "none"
```

但不是所有命令都是可以配置的，所有可配置项[在这](https://doc.rust-lang.org/stable/cargo/reference/config.html#configuration-format)