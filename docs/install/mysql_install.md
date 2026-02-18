# 离线部署 MySQL

::: info 测试设备说明

测试机型：联想 开天 M99h G1t（海光 C86 3250）

测试系统：银河麒麟 V10 SP1 2503

最后更新时间：2026-02-16

:::

## 一、准备工作

### 拉取并导出镜像

- 在连接外网的计算机中执行以下命令：

```shell
# 拉取mysql镜像
docker pull mysql:8.4.8 --platform linux/amd64
# 拉取phpmyadmin镜像方便管理数据库
docker pull phpmyadmin:5.2.3 --platform linux/amd64

# 导出镜像到指定位置
docker save -o /Users/admin/Downloads/database.tar mysql:8.4.8 phpmyadmin:5.2.3

# 删除镜像
docker image prune
```

### 编辑 docker-compose.yml

- 创建文本文件写入以下内容并保存为 `docker-compose.yml` ：

```yaml:line-numbers
services:
  mysql:
    image: mysql:8.4.8
    container_name: mysql
    restart: always
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=123456  # 数据库root密码（必改）
      - MYSQL_ROOT_HOST=%           # 允许所有主机访问root用户
      - TZ=Asia/Shanghai            # 时区设定
      - LANG=C.UTF-8                # 确保字符集正常
    volumes:
      - mysql_data:/var/lib/mysql
      - ./mysql/conf:/etc/mysql/conf.d  # 自定义配置文件（解决字符集/插件兼容问题）
    command:
      --sql-mode="STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION"
      --max_connections=1000
      --character-set-server=utf8mb4
      --collation-server=utf8mb4_unicode_ci

  phpmyadmin:
    image: phpmyadmin:5.2.3
    container_name: phpmyadmin
    restart: always
    ports:
      - "8080:80"
    environment:
      - PMA_HOST=mysql               # 关联MySQL服务名
      - PMA_PORT=3306                # MySQL端口
      - PMA_ARBITRARY=1              # 允许连接任意MySQL服务器
      - TZ=Asia/Shanghai             # 时区配置
      - UPLOAD_LIMIT=100M            # 上传文件大小限制
      - PMA_TIMEOUT=60               # 延长连接超时，适配MySQL启动耗时
    depends_on:
      - mysql

volumes:
  mysql_data:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: /opt/mysql/data  # mysql持久化目录
```

## 二、在内网计算机中部署

1. 将 `database.tar` 与 `docker-compose.yml` 拷贝至内网计算机中，在文件目录执行命令：

```shell
# 导入镜像
docker load -i ./database.tar

# 创建持久化目录
mkdir -p /opt/mysql/data

# 拷贝docker-compose.yml
cp ./docker-compose.yml /opt/mysql/docker-compose.yml

# 启动
docker compose up -d
```

1. 访问 [127.0.0.1:8080](http://127.0.0.1:8080/) 打开 phpmyadmin 管理面板。

------

欢迎同为国网一线班组的同僚们来信交流：[badzhang@j1net.org](mailto:badzhang@j1net.org)