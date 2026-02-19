# MySQL 安装并创建数据库

::: info ⚠️ 注意

从本篇开始，就要切换到我们要部署应用服务的内网计算机中了

测试机型：联想 开天 M99h G1t（海光 C86 3250）

测试系统：银河麒麟 V10 SP1 2503

:::

## 启动 MySQL 与 phpMyAdmin

准备工作中应该已经将 `apps.tar` `mysql.yml` `rustfs.yml` `onlyoffice.yml` `cloudreve.yml` 5个文件拷贝进来，以银河麒麟举例，导入到数据盘根目录中时，文件所在路径一般为 `/data/usershare/` 后续命令以此为基础，如路径不一致替换即可

**打开终端**

::: warning 建议先提权

执行命令时如果提示权限不足，需执行 `sudo -i` 提权，或在命令前加 `sudo`

:::

```shell
# 导入镜像包
docker load -i /data/usershare/apps.tar

# 创建 MySQL、phpmyadmin 安装路径和持久化路径
mkdir -p /opt/mysql/data

# 拷贝 docker-compose.yml 文件
cp /data/usershare/mysql.yml /opt/mysql/docker-compose.yml

# 进入安装目录
cd /opt/mysql

# 启动
docker compose up -d

# （可选）查看实时日志
docker compose logs -f
```

## 创建数据库、用户

### 登录数据库

浏览器访问 [127.0.0.1:8080](http://127.0.0.1:8080) 打开 phpMyAdmin

填写服务器 `mysql` 、用户名 `root` 、密码 `编写配置文件时设置的密码`

::: tip 提示

密码为 mysql.yml 中应由你修改的 `MYSQL_ROOT_PASSWORD` 字段

```yaml{2}:line-numbers=8
environment:
  - MYSQL_ROOT_PASSWORD=123456  # 数据库root密码（必改）
  - MYSQL_ROOT_HOST=%           # 允许所有主机访问root用户
  - TZ=Asia/Shanghai            # 时区设定
  - LANG=C.UTF-8                # 确保字符集正常
```

上述配置中密码即为 `123456`

:::

### 创建数据库及用户

登录进 MySQL 数据库后按图示创建数据库及用户

![创建数据库](/create_db/create_db.webp)

如图：数据库用户名 `cloudreve` 、用户密码 `716yR9(Yu[)oM4DK` 、且创建了同名数据库 `cloudreve`

::: danger ⚠️ 需与 Cloudreve 的 docker-compose.yml 保持一致

填入到 Cloudreve 配置文件 `cloudreve.yml` 中的相应位置，顺便把 IP 改为宿主机 IP

```yaml:line-numbers=10
environment:
  - TZ=Asia/Shanghai
  - CR_CONF_Database.Type=mysql
  - CR_CONF_Database.Host=192.168.1.165         # 数据库所在IP地址 // [!code focus:4]
  - CR_CONF_Database.User=cloudreve             # 用户名
  - CR_CONF_Database.Password=716yR9(Yu[)oM4DK  # 用户密码
  - CR_CONF_Database.Name=cloudreve             # 数据库名
  - CR_CONF_Database.Port=3306
  - CR_CONF_Redis.Server=redis:6379
```

