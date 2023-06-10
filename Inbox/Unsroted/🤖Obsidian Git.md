---
title: 🤖Obsidian Git
date created: 06/10-2023, 21:51, 星期六
date modified: 06/10-2023, 21:51, 星期六
---

[[Obsidian]]的[[第三方插件]]

项目仓库在这：[denolehov/obsidian-git: Backup your Obsidian.md vault with git (github.com)](https://github.com/denolehov/obsidian-git)

直接安装无需特别配置，第一次安装好后需要执行 `Obsidian Git: Initialize a new repo` 

## 无法添加远程仓库

我使用的版本是 `2.20.3`，在接入远程仓库时出了点问题，通过 `Ctrl + P` 执行 Edit remotes 来添加远程仓库时
![](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20230610215635.png)
这边提示可以通过输入新的 remote 名称来添加远程库
![](https://vercel-proxy.norah1to.com/proxy/raw.githubusercontent.com/NoraH1to/cdn/master/img/20230610215856.png)
然而实际上是行不通的，当你输入一个不存在的远程库尝试添加时，它会提示 `error: No such remote 'xxx'`
