# 离线部署 Cloudreve Pro

::: info 测试设备说明

测试机型：联想 开天 M99h G1t（海光 C86 3250）

测试系统：银河麒麟 V10 SP1 2503

最后更新时间：2026-02-16

:::

## 一、前言

### Cloudreve 是什么？

Cloudreve 可以让您快速搭建起公私兼备的网盘系统。Cloudreve 在底层支持不同的云存储平台，用户在实际使用时无须关心物理存储方式。你可以使用 Cloudreve 搭建个人用网盘、文件分享系统，亦或是针对大小团体的公有云系统。

### 内网环境下的使用场景？

作者所在单位负责输电线路的运维工作，会使用无人机拍取大量照片，普通班组并未配备 NAS 之类的存储设备，所以想到在多台计算机中部署 RustFS 作为 S3 存储桶（非集群部署，方便新计算机投入使用时添加存储策略即可，也方便后续旧计算机更换时的迁移，更重要的是我不会集群部署0.0），并在常用电脑中部署 Cloudreve 连接至各存储桶，以提供各种便利：

- 查找资料、照片无需在各个计算机中流转，任一计算机都可访问全部资料；
- 部署 OnlyOffice 后多台计算机可同时在线编辑，实现协同办公（在线文档）的功能；
- 邮件传输大文件时，可直接分享 Cloudreve 链接，文件流转效率大大提升；

……

> 由于涉及多台计算机作为 S3 存储桶，会在 Cloudreve 中添加多个存储策略以供使用，所以作者推荐且本文部署的是捐助版也就是 Pro 版本，若要部署社区版将镜像替换为社区版的镜像即可

## 二、准备工作

### 拉取并导出镜像

- 登录 Cloudreve 授权管理平台获取登录私有仓库命令：

![img](https://docs.cos.j1net.com/halodoc/cloudreve_install/login.webp)

- 在一台接入互联网、并安装有 docker 的计算机中拉取镜像（推荐挂代理，否则下载速度会慢）：

使用上一步获取的 **登录命令** 登录到官方的私有仓库，登录后执行以下命令：

```shell
# 拉取cloudreve镜像，注意需对应内网计算机的处理器架构，社区版将镜像替换为：cloudreve/cloudreve:latest
docker pull cloudreve.azurecr.io/cloudreve/pro:4.14.1 --platform linux/amd64
# 拉取redis镜像
docker pull redis:8.4.0 --platform linux/amd64
# 拉取tika镜像用于全文搜索（不需要全文搜索可跳过）
docker pull apache/tika:latest --platform linux/amd64
# 拉取meilisearch镜像用于全文搜索（不需要全文搜索可跳过）
docker pull getmeili/meilisearch:v1.35.0 --platform linux/amd64

# 导出镜像并保存至指定路径
docker save -o /Users/admin/Downloads/all.tar cloudreve.azurecr.io/cloudreve/pro:4.14.1 redis:8.4.0 apache/tika:latest getmeili/meilisearch:v1.35.0

# 删除镜像，以免占用外网计算机空间
docker rmi cloudreve.azurecr.io/cloudreve/pro:4.14.1 redis:8.4.0 apache/tika:latest getmeili/meilisearch:v1.35.0
# 或使用命令删除所有未使用的资源、镜像
docker image prune -a -f
docker system prune
```

> 条件有限无法获取镜像的检一网络科技可免费提供普通版 .tar 包（为保护 Cloudreve 作者权益，Pro 版需提供授权截图可打码/其余可证明有授权的方式/i国网），邮件发送“cloudreve镜像请求”到 [official@j1net.com](mailto:official@j1net.com)

### 编辑 docker-compose.yml

- 创建文本文件写入以下内容并保存为 `docker-compose.yml` ：

```yaml
services:
  cloudreve:
    image: "cloudreve.azurecr.io/cloudreve/pro:4.14.1"  # 替换为cloudreve镜像名，防止出错加引号
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
      - CR_CONF_Database.User=root        # 此处需修改为数据库用户名（必改）
      - CR_CONF_Database.Password=123456  # 此处需修改为数据库密码（必改）
      - CR_CONF_Database.Name=cloudreve   # 此处需修改为数据库名（必改）
      - CR_CONF_Database.Port=3306        # 此处需修改为数据库端口（必改）
      - CR_CONF_Redis.Server=redis:6379   # 本配置包含Redis部署，如使用外部Redis才需修改此项
      - CR_LICENSE_KEY=                   # 授权密钥，社区版可删除此行（Pro版必填）
      - CR_OFFLINE_LICENSE=               # 离线许可证，社区版可删除此行（Pro版必填）
    volumes:
      - cloudreve_data:/cloudreve/data

  redis:
    image: redis:8.4.0  # 替换为redis镜像名
    container_name: redis
    restart: always
    volumes:
      - redis_data:/data
    command: redis-server --appendonly yes

  tika:
    image: apache/tika:latest  # 替换为tika镜像名
    container_name: tika
    restart: always
    ports:
      - 9998:9998  # tika映射端口

  meilisearch:
    image: getmeili/meilisearch:v1.35.0  # 替换为meilisearch镜像名
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

- 注意其中部分内容需修改，已注释标明，授权密钥和离线许可证需登录授权中心获取：

![img](https://docs.cos.j1net.com/halodoc/cloudreve_install/key.webp)

## 三、在内网计算机中部署

- 将 `all.tar` 、`docker-compose.yml` 文件拷贝至内网计算机中，例如 `/data/usershare` 目录中，以 `root` 用户执行命令：

```shell
# 导入镜像
docker load -i /data/usershare/all.tar

# 创建持久化目录
mkdir -p /opt/cloudreve/data /opt/cloudreve/redis/data /opt/cloudreve/meili/data

# 拷贝docker-compose.yml
cp /data/usershare/docker-compose.yml /opt/cloudreve/docker-compose.yml

# 启动
docker compose up -d
```

- 本机访问 [127.0.0.1](http://127.0.0.1/) 或在其他局域网计算机中访问本机 IP。

------

欢迎同为国网一线班组的同僚们来信交流：[official@j1net.com](mailto:official@j1net.com)