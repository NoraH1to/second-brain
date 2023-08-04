---
title: cargo 配置
date created: 2023-08-04
date modified: 2023-08-04
---

## 配置文件

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