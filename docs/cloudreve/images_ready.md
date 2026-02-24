# 准备镜像、配置文件

以下步骤需在有网络连接、安装有docker的计算机中进行，该计算机处理器架构与部署应用的宿主机处理器架构相同时，执行 `同架构` 命令，不同时根据部署应用的宿主机处理器架构选择 `amd64` 或 `arm64`

::: tip 国网员工友情提供

没有条件的可以联系作者提供所有下述部署获取的镜像和配置文件，支持国网公司内网邮件发送

联系方式：[badzhang@j1net.org](mailto:badzhang@j1net.org)

:::

## 拉取并导出镜像

### 拉取镜像 {#dockerpull}

打开终端

::: code-group

```shell [同架构]
# 拉取镜像（全部）
docker pull mysql:8.4.8 ; \
docker pull phpmyadmin:5.2.3 ; \
docker pull rustfs/rustfs:1.0.0-alpha.83 ; \
docker pull moqisoft/documentserver:9.2.1 ; \
docker pull redis:8.6.0 ; \
docker pull cloudreve/cloudreve:4.14.1 ; \
docker pull apache/tika:3.2.3.0 ; \
docker pull getmeili/meilisearch:v1.35.1
```

```shell [amd64]
# 拉取镜像（全部）并添加参数指定拉取 amd64 架构
docker pull --platform linux/amd64 mysql:8.4.8 ; \
docker pull --platform linux/amd64 phpmyadmin:5.2.3 ; \
docker pull --platform linux/amd64 rustfs/rustfs:1.0.0-alpha.83 ; \
docker pull --platform linux/amd64 moqisoft/documentserver:9.2.1 ; \
docker pull --platform linux/amd64 redis:8.6.0 ; \
docker pull --platform linux/amd64 cloudreve/cloudreve:4.14.1 ; \
docker pull --platform linux/amd64 apache/tika:3.2.3.0 ; \
docker pull --platform linux/amd64 getmeili/meilisearch:v1.35.1
```

```shell [arm64]
# 拉取镜像（全部）并添加参数指定拉取 arm64 架构
docker pull --platform linux/arm64 mysql:8.4.8 ; \
docker pull --platform linux/arm64 phpmyadmin:5.2.3 ; \
docker pull --platform linux/arm64 rustfs/rustfs:1.0.0-alpha.83 ; \
docker pull --platform linux/arm64 moqisoft/documentserver:9.2.1 ; \
docker pull --platform linux/arm64 redis:8.6.0 ; \
docker pull --platform linux/arm64 cloudreve/cloudreve:4.14.1 ; \
docker pull --platform linux/arm64 apache/tika:3.2.3.0 ; \
docker pull --platform linux/arm64 getmeili/meilisearch:v1.35.1
```

:::

以上包含 数据库（MySQL）、后端存储（RustFS）、在线协作（OnlyOffice）、Cloudreve（附带全文搜索） 四个模块所需的全部镜像，可以修改 `:` 后的版本号，如需单独拉取某一模块的镜像参考下表删去其余命令即可，也可一行一行的执行（删去后面的 `; \` ）

::: details 各模块所需镜像

| 模块                           | 所需镜像                                                     |
| ------------------------------ | ------------------------------------------------------------ |
| 数据库<br>（MySQL）            | `mysql:8.4.8` `phpmyadmin:5.2.3`                             |
| 后端存储（RustFS）             | `rustfs/rustfs:1.0.0-alpha.83`                               |
| 在线协作<br/>（OnlyOffice）    | `moqisoft/documentserver:9.2.1`                              |
| Cloudreve<br/>（附带全文搜索） | `redis:8.6.0` `cloudreve/cloudreve:4.14.1` `apache/tika:3.2.3.0` `getmeili/meilisearch:v1.35.1` |
| Cloudreve<br/>（不带全文搜索） | `redis:8.6.0` `cloudreve/cloudreve:4.14.1`                   |

:::

检查镜像是否全部拉取成功

```shell
docker images
```

### 导出镜像

将刚下载的镜像导出到指定位置，例如 `/Users/badzhang/Downloads` ，请根据上一步下载的镜像数量、版本号适当修改或直接复制

::: code-group

```shell [同架构]:line-numbers
# 注意修改最后一行的保存路径
docker save \
mysql:8.4.8 \
phpmyadmin:5.2.3 \
rustfs/rustfs:1.0.0-alpha.83 \
moqisoft/documentserver:9.2.1 \
redis:8.6.0 \
cloudreve/cloudreve:4.14.1 \
apache/tika:3.2.3.0 \
getmeili/meilisearch:v1.35.1 \
-o /Users/badzhang/Downloads/apps.tar
```

```shell [amd64]:line-numbers
# 注意修改最后一行的保存路径
docker save \
--platform linux/amd64 \
mysql:8.4.8 \
phpmyadmin:5.2.3 \
rustfs/rustfs:1.0.0-alpha.83 \
moqisoft/documentserver:9.2.1 \
redis:8.6.0 \
cloudreve/cloudreve:4.14.1 \
apache/tika:3.2.3.0 \
getmeili/meilisearch:v1.35.1 \
-o /Users/badzhang/Downloads/apps.tar
```

```shell [arm64]:line-numbers
# 注意修改最后一行的保存路径
docker save \
--platform linux/arm64 \
mysql:8.4.8 \
phpmyadmin:5.2.3 \
rustfs/rustfs:1.0.0-alpha.83 \
moqisoft/documentserver:9.2.1 \
redis:8.6.0 \
cloudreve/cloudreve:4.14.1 \
apache/tika:3.2.3.0 \
getmeili/meilisearch:v1.35.1 \
-o /Users/badzhang/Downloads/apps.tar
```

## 编写 docker-compose.yml {#dcyml}

创建 **4** 个文本文件分别命名为 `mysql.yml` `rustfs.yml` `onlyoffice.yml` `cloudreve.yml`

::: tip 叠甲保护

小白建议跟着作者命名，后续可以直接复制命令执行，专业人士轻喷 QAQ

:::

给对应文件写入 docker compose 配置

::: code-group

```yaml [MySQL]:line-numbers
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
      - PMA_HOST=mysql              # 关联MySQL服务名
      - PMA_PORT=3306               # MySQL端口
      - PMA_ARBITRARY=1             # 允许连接任意MySQL服务器
      - TZ=Asia/Shanghai            # 时区配置
      - UPLOAD_LIMIT=100M           # 上传文件大小限制
      - PMA_TIMEOUT=60              # 延长连接超时，适配MySQL启动耗时
    depends_on:
      - mysql

volumes:
  mysql_data:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: /opt/mysql/data  # mysql持久化目录，可自行修改
```

```yaml [RustFS]:line-numbers
services:
  rustfs:
    image: rustfs/rustfs:1.0.0-alpha.83
    container_name: rustfs
    security_opt:
      - "no-new-privileges:true"  # 禁止容器提升自身权限
    ports:
      - "9000:9000"  # S3 API 对外端口
      - "9001:9001"  # 控制台对外端口
    environment:
      - RUSTFS_VOLUMES=/data/rustfs0           # 数据卷（多个路径用逗号分隔）
      - RUSTFS_ADDRESS=0.0.0.0:9000            # API监听地址
      - RUSTFS_CONSOLE_ADDRESS=0.0.0.0:9001    # 控制台监听地址
      - RUSTFS_CONSOLE_ENABLE=true             # 开启控制台交互
      - RUSTFS_CORS_ALLOWED_ORIGINS=*          # S3 API放开来源
      - RUSTFS_CONSOLE_CORS_ALLOWED_ORIGINS=*  # 控制台放开来源
      - RUSTFS_ACCESS_KEY=admin                # 控制台账号（必改）
      - RUSTFS_SECRET_KEY=123456               # 控制台密码（必改）
      - RUSTFS_OBS_LOGGER_LEVEL=info           # 日志级别

    volumes:
      - ./deploy/data/pro:/data  # 持久化配置目录
      - ./deploy/logs:/app/logs  # 日志目录
      - /data/usershare/共享目录:/data/rustfs0  # 存储目录（必改）

    networks:
      - rustfs-network
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "sh", "-c", "curl -f http://localhost:9000/health && curl -f http://localhost:9001/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
```

```yaml [OnlyOffice]:line-numbers
services:
  onlyoffice:
    image: moqisoft/documentserver:9.2.1
    container_name: onlyoffice
    ports:
      - "40156:80"    # 服务端口
      - "40157:8000"  # 授权查看端口（无API需求可以无视）
    restart: always
    privileged: true
    environment:
      - ALLOW_PRIVATE_IP_ADDRESS=true  # 允许内网地址
      - JWT_ENABLED=true               # 开启JWT密钥验证
      - JWT_SECRET=secret_m5fR6A       # JWT密钥设置（必改）
      - JWT_HEADER=Authorization       # 指定客户端传递JWT令牌的HTTP请求头名称
      - JWT_IN_BODY=true               # 允许在HTTP请求体中传递JWT令牌
      - WOPI_ENABLED=true              # 开启WOPI协议支持
    volumes:
      - /host/path/to/Data:/var/www/onlyoffice/Data
      - /host/path/to/App_Data:/var/www/onlyoffice/App_Data
      - /host/path/to/sdkjs-plugins:/var/www/onlyoffice/documentserver/sdkjs-plugins
      - /proc/cpuinfo:/host/proc/cpuinfo
      - /sys/class:/host/sys/class
    tty: true
    stdin_open: true
```

```yaml [Cloudreve]:line-numbers
services:
  cloudreve:
    image: cloudreve/cloudreve:4.14.1
    container_name: cloudreve
    depends_on:
      - redis
    restart: always
    ports:
      - 80:5212  # cloudreve映射端口
    environment:
      - TZ=Asia/Shanghai
      - CR_CONF_Database.Type=mysql
      - CR_CONF_Database.Host=127.0.0.1   # 此处需修改为MySQL数据库地址（必改）
      - CR_CONF_Database.User=cloudreve   # 此处需修改为数据库用户名（必改）建议用root之外的用户
      - CR_CONF_Database.Password=123456  # 此处需修改为数据库密码（必改）
      - CR_CONF_Database.Name=cloudreve   # 此处需修改为数据库名（必改）
      - CR_CONF_Database.Port=3306        # 数据库端口（mysql默认为3306）
      - CR_CONF_Redis.Server=redis:6379   # 本配置包含Redis部署，如使用外部Redis才需修改此项
    volumes:
      - cloudreve_data:/cloudreve/data

  redis:
    image: redis:8.6.0  # 替换为redis镜像名
    container_name: redis
    restart: always
    volumes:
      - redis_data:/data
    command: redis-server --appendonly yes

  tika:
    image: apache/tika:3.2.3.0  # 替换为tika镜像名
    container_name: tika
    restart: always
    ports:
      - 9998:9998  # tika映射端口

  meilisearch:
    image: getmeili/meilisearch:v1.35.1  # 替换为meilisearch镜像名
    container_name: meilisearch
    restart: always
    ports:
      - 7700:7700  # meilisearch映射端口
    environment:
      - MEILI_MASTER_KEY='TmDSKZhcoEMzTt3STPhRQVRxPEVZw7m2TymKwKhs_YaL3GF'  # 请替换为实际的API密钥
    volumes:
      - meili_data:/meili_data

volumes:
  cloudreve_data:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: /opt/cloudreve/data        # cloudreve持久化目录
  redis_data:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: /opt/cloudreve/redis/data  # redis持久化目录
  meili_data:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: /opt/cloudreve/meili/data  # meilisearch持久化目录
```

```yaml [Cloudreve Pro（需购买授权）]:line-numbers
services:
  cloudreve:
    image: "cloudreve.azurecr.io/cloudreve/pro:4.14.1"
    container_name: cloudreve
    depends_on:
      - redis
    restart: always
    ports:
      - 80:5212  # cloudreve映射端口
    environment:
      - TZ=Asia/Shanghai
      - CR_CONF_Database.Type=mysql
      - CR_CONF_Database.Host=127.0.0.1           # 此处需修改为MySQL数据库地址（必改）
      - CR_CONF_Database.User=cloudreve           # 此处需修改为数据库用户名（必改）建议用root之外的用户
      - CR_CONF_Database.Password=123456          # 此处需修改为数据库密码（必改）
      - CR_CONF_Database.Name=cloudreve           # 此处需修改为数据库名（必改）
      - CR_CONF_Database.Port=3306                # 数据库端口（mysql默认为3306）
      - CR_CONF_Redis.Server=redis:6379           # 本配置包含Redis部署，如使用外部Redis才需修改此项
      - CR_LICENSE_KEY=${CR_LICENSE_KEY}          # 授权密钥
      - CR_OFFLINE_LICENSE=&{CR_OFFLINE_LICENSE}  # 离线许可证
    volumes:
      - cloudreve_data:/cloudreve/data

  redis:
    image: redis:8.6.0  # 替换为redis镜像名
    container_name: redis
    restart: always
    volumes:
      - redis_data:/data
    command: redis-server --appendonly yes

  tika:
    image: apache/tika:3.2.3.0  # 替换为tika镜像名
    container_name: tika
    restart: always
    ports:
      - 9998:9998  # tika映射端口

  meilisearch:
    image: getmeili/meilisearch:v1.35.1  # 替换为meilisearch镜像名
    container_name: meilisearch
    restart: always
    ports:
      - 7700:7700  # meilisearch映射端口
    environment:
      - MEILI_MASTER_KEY='TmDSKZhcoEMzTt3STPhRQVRxPEVZw7m2TymKwKhs_YaL3GF'  # 请替换为实际的API密钥
    volumes:
      - meili_data:/meili_data

volumes:
  cloudreve_data:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: /opt/cloudreve/data        # cloudreve持久化目录
  redis_data:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: /opt/cloudreve/redis/data  # redis持久化目录
  meili_data:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: /opt/cloudreve/meili/data  # meilisearch持久化目录
```

## 导入文件

如按上述步骤执行，应有 **1** 个镜像包和 **4** 个配置文件 `apps.tar` `mysql.yml` `rusts.yml` `onlyoffice.yml` `cloudreve.yml` ，全部拷贝至目标内网计算机中，至此准备工作就全部完成啦，后续步骤全部在目标计算机中执行