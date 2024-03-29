---
title: cargo 配置
date created: 2023-08-04
date modified: 2023-08-04
---

## 可配置项

[Configuration - The Cargo Book (rust-lang.org)](https://doc.rust-lang.org/stable/cargo/reference/config.html#configuration-format)

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

## 配置 crates.io 镜像源

`crates.io` 是默认的源仓库，国内对于这种仓库配置镜像源是基操了

添加如下配置：

```toml
[source.crates-io]
replace-with = 'tuna'

# 清华大学镜像
[source.tuna]
registry = "https://mirrors.tuna.tsinghua.edu.cn/git/crates.io-index.git"
```

随便引入一个包，`cargo build` 一下，可以看到走的是镜像

![](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20230804142746.png)

上面配置中通过 `[source.<name>]` 的形式新增了一个名为 `tuna` 的源，并用 `replace-with` 替换了默认源，具体看文档：[Source Replacement - The Cargo Book (rust-lang.org)](https://doc.rust-lang.org/stable/cargo/reference/source-replacement.html)

## 配置初始化版本管理工具行为

执行 `cargo new` 会默认初始化 Git 仓库，如果我们不想初始化任何版本管理工具，需要加上参数 `cargo new --vcs=none`

如果需要将 `vcs` 默认值改为 `none` ，我们需要添加如下配置：

```toml
[cargo-new]
vcs = "none"
```