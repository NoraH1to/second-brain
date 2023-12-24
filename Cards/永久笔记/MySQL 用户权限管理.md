---
title: MySQL 用户权限管理
date created: 2023-12-24
date modified: 2023-12-24
---

## 用户授权命令

```sql
GRANT 权限1,权限2,…权限n ON 数据库名称.表名称 TO 用户名@用户地址 [IDENTIFIED BY ‘密码口令’];
```

- **用户地址**为 `"%"` 时为授予**通过网络访问**的该用户权限

- 若用户不存在，则会新建一个用户（需要加上 `IDENTIFIED BY '密码口令'`）

## 收回权限命令

基本同上

```sql
REVOKE 权限1,权限2,…权限n ON 数据库名称.表名称 FROM 用户名@用户地址;
```

> [!WARNING] 注意
> 重新登陆后生效

## 给予全部权限

将权限替换成 `ALL PRIVILEGES` 即可：

```sql
GRANT ALL PRIVILEGES ON 数据库名称.表名称 TO 用户名@用户地址 [IDENTIFIED BY ‘密码口令’];
```

## 查看某用户所有权限

```sql
SHOW GRANTS FOR 用户名@用户地址 ;
```