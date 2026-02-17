# 离线部署 OnlyOffice

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
docker pull moqisoft/documentserver:9.2.1 --platform linux/amd64

# 导出镜像到指定位置
docker save -o /Users/admin/Downloads/onlyoffice.tar moqisoft/documentserver:9.2.1

# 删除镜像
docker image prune
```

### 编辑 docker-compose.yml

- 创建文本文件写入以下内容并保存为 `docker-compose.yml` ：

```yaml
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

## 二、在内网计算机中部署

- 将 `onlyoffice.tar` 与 `docker-compose.yml` 拷贝至内网计算机中，在文件目录执行命令：

```shell
# 导入镜像
docker load -i ./onlyoffice.tar

# 创建应用docker compose目录
mkdir -p /opt/onlyoffice

# 拷贝docker-compose.yml
cp ./docker-compose.yml /opt/onlyoffice/docker-compose.yml

# 启动
docker compose up -d
```

------

欢迎同为国网一线班组的同僚们来信交流：[badzhang@j1net.org](mailto:badzhang@j1net.org)