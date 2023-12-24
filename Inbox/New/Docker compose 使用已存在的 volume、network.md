
**docker-compose** 的目的是将所有配置集成在一起，以方便可以随时开箱使用构建容器，因此配置的内容必须足够**纯粹**，如果有yi

如果需要在 `docker-compose.yml` 中使用 docker 中先前定义好的数据字段或网络，需要表明其来自外部：

```yml
version: '3'
services:
  mysql:
    image: mysql
    container_name: mysql
    restart: always
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=root
    volumes:
      - type: volume
        source: mysql_conf # 外部字段
        target: /etc/mysql/conf.d
      - type: volume
        source: mysql_data # 外部字段
        target: /var/lib/mysql
    networks:
      - nextcloud_net # 外部网络

# 定义网络来自外部
networks:
  nextcloud_net:
    external: true

# 定义网络来自外部
volumes:
  mysql_conf:
    external: true
  mysql_data:
    external: true
```