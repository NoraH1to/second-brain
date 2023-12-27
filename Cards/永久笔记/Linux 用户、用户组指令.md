---
title: Linux 用户、用户组指令
date created: 2023-12-24
date modified: 2023-12-27
---

## 添加组

### groupadd

```bash
sudo groupadd 组名
```

## 删除组

### groupdel

```bash
sudo groupdel 组名
```

## 组添加用户

### usermod

```bash
usermod -aG 组名 用户名
```

### gpasswd

```bash
gpasswd -a 用户名 组名
```

## 组删除用户

### gpasswd

```bash
gpasswd -d 用户名 组名
```