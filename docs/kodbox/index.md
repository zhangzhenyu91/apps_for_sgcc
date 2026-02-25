# 快速开始

::: info ⚠️ 注意

从本篇开始，就要切换到我们要部署应用服务的内网计算机中了

测试机型：联想 开天 M99h G1t（海光 C86 3250）

测试系统：银河麒麟 V10 SP1 2503

:::

## 启动 MySQL 与 phpMyAdmin

准备工作中应该已经将 `apps.tar` `mysql.yml` `rustfs.yml` `onlyoffice.yml` `cloudreve.yml` 5个文件拷贝进来，以银河麒麟举例，导入到数据盘根目录中时，文件所在路径一般为 `/data/usershare/` 后续命令以此为基础，如路径不一致替换即可

**打开终端**

::: tip 建议先提权

执行命令时如果提示权限不足，需执行 `sudo -i` 提权，或在命令前加 `sudo`

:::

```shell
# 创建容器网络 app-net
docker network create app-net

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

密码为 [`mysql.yml`](./images_ready.md#dcyml) 中应由你修改的 `MYSQL_ROOT_PASSWORD` 字段

```yaml:line-numbers=8
environment:
  - MYSQL_ROOT_PASSWORD=123456  # 数据库root密码（必改） // [!code focus]
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

::: tip ⚠️ 需与 Cloudreve 的 docker-compose.yml 保持一致

填入到 [`cloudreve.yml`](./images_ready.md#dcyml) 中的相应位置，顺便把 IP 改为宿主机 IP

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

:::

## 启动 Cloudreve

**打开终端**

::: tip 建议先提权

执行命令时如果提示权限不足，需执行 `sudo -i` 提权，或在命令前加 `sudo`

:::

::: code-group

```shell [社区版]
# 创建安装目录和持久化路径
mkdir -p /opt/cloudreve/data /opt/cloudreve/redis/data /opt/cloudreve/meili/data

# 拷贝 docker-compose.yml 文件
cp /data/usershare/cloudreve.yml /opt/cloudreve/docker-compose.yml

# 进入安装目录
cd /opt/cloudreve

# 启动
docker compose up -d

# （可选）查看实时日志
docker compose logs -f
```

```shell [Pro 版]
# 创建安装目录和持久化路径
mkdir -p /opt/cloudreve/data /opt/cloudreve/redis/data /opt/cloudreve/meili/data

# 拷贝 docker-compose.yml 文件
cp /data/usershare/cloudreve.yml /opt/cloudreve/docker-compose.yml

# 进入安装目录
cd /opt/cloudreve

# 设置授权密钥和离线密钥
export CR_LICENSE_KEY="你的授权密钥"
export CR_OFFLINE_LICENSE="你的离线许可证密钥"

# 启动
docker compose up -d

# （可选）查看实时日志
docker compose logs -f
```

:::

浏览器访问 [127.0.0.1](http://127.0.0.1) 打开 Cloudreve （http协议80端口可以省略）

::: tip 其他局域网计算机访问时需使用宿主机 IP

例：[http://192.168.1.165](http://192.168.1.165)

:::

点击 `立即注册` ，首个注册的用户即为 `管理员` ，经作者测试，可能由于公司内网邮箱 SMTP 连接方式不设加密等原因，Cloudreve 暂时无法使用内网邮箱 SMTP 服务发送各类通知邮件，等待开发团队优化吧 🙏
