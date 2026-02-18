# 银河麒麟系统离线安装 docker

::: info 测试环境说明

测试机处理器：i5-4460

测试系统镜像：银河麒麟 V10 SP1 2503

最后更新时间：2026-02-17

:::

## 一、下载软件包

在 [官方下载站](https://download.docker.com/linux/) 下载对应处理器架构的 `containerd.io`、`docker-ce-cli`、`docker-ce`、`docker-compose-plugin` 4个软件包（最新版本在离线安装时可能有依赖问题，建议使用较为稳定的旧版）

::: tip 已经作者测试的包

[坏比弓长的分享｜检一网络 -- 云盘](https://cloud.j1net.com/s/LKF8)

:::

将上述4个文件拷贝至内网计算机中

![下载软件包](/docker_install/download.webp)

## 二、依次安装 deb 包

打开文件所在目录，`右击` → `打开终端`

![打开终端](/docker_install/openshell.webp)

### 安装 containerd.io

```shell
# 注意替换安装包名称，使用作者提供的包可直接复制命令执行
sudo dpkg -i containerd.io_1.7.27-1_amd64.deb
```

提示输入密码时输入计算机 `登录密码` 后按 `Enter` ，弹出安全中心提醒时选择 `允许安装` ，完成后如下图

![containerd安装完成](/docker_install/container.io.webp)

### 安装 docker-ce-cli

```shell
sudo dpkg -i docker-ce-cli_26.1.4-1~debian.11~bullseye_amd64.deb
```

![docker-ce-cli安装完成](/docker_install/docker-ce-cli.webp)

### 安装 docker-ce

```shell
sudo dpkg -i docker-ce_26.1.4-1~debian.11~bullseye_amd64.deb
```

![docker-ce安装完成](/docker_install/docker-ce.webp)

### 安装 docker-compose-plugin

```shell
sudo dpkg -i docker-compose-plugin_2.40.3-1~debian.11~bullseye_amd64.deb
```

![docker-compose-plugin安装完成](/docker_install/docker-compose-plugin.webp)

## 三、检查 docker 状态

```shell
systemctl status docker
```

![docker检查](/docker_install/docker-check.webp)

```shell
docker compose version
```

![docker-compose检查](/docker_install/docker-compose-check.webp)

------

欢迎同为国网一线班组的同僚们来信交流：[badzhang@j1net.org](mailto:badzhang@j1net.org)
