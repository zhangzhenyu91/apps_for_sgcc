# 离线部署 RustFS

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
docker pull rustfs/rustfs:latest --platform linux/amd64

# 导出镜像到指定位置
docker save -o /Users/admin/Downloads/rustfs.tar rustfs/rustfs:latest

# 删除镜像
docker image prune
```

### 编辑 docker-compose.yml

- 创建文本文件写入以下内容并保存为 `docker-compose.yml` ：

```yaml
services:
  rustfs:
    image: rustfs/rustfs:latest
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

# docker network ls列出网络，请填写一个不冲突的或删除network段
networks:
  rustfs-network:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/16
```

## 二、在内网计算机中部署

- 将 `rustfs.tar` 、`docker-compose.yml` 文件拷贝至内网计算机中，例如 `/data/usershare` 目录中，以 `root` 用户执行命令：

```shell
# 导入镜像
docker load -i /data/usershare/rustfs.tar

# 创建目录
mkdir -p /opt/rustfs/deploy/data/pro /opt/rustfs/deploy/logs /data/usershare/共享目录

# 拷贝docker-compose.yml
cp /data/usershare/docker-compose.yml /opt/rustfs/docker-compose.yml

# 启动
docker compose up -d
```

- 本机访问 [127.0.0.1:9001](http://127.0.0.1:9001/) 打开控制台。

------

欢迎同为国网一线班组的同僚们来信交流：[official@j1net.com](mailto:official@j1net.com)