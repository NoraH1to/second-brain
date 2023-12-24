
## 用户授权命令

```sql
GRANT 权限1,权限2,…权限n ON 数据库名称.表名称 TO 用户名@用户地址 [IDENTIFIED BY ‘密码口令’];
```

**用户地址**

若用户不存在，则会新建一个用户（需要加上 `IDENTIFIED BY '密码口令'`）

## 给予全部权限

```sql
GRANT ALL PRIVILEGES ON *.* TO 用户名@用户地址 IDENTIFIED BY '123';
```