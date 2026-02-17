# Docker 环境准备

::: info 测试设备说明

测试机型：联想 开天 M99h G1t（海光 C86 3250）

测试系统：银河麒麟 V10 SP1 2503

最后更新时间：2026-02-16

:::

## 一、下载软件包

https://download.docker.com/linux/

依据测试机型路径为 `linux/debian/dists/bookworm/pool/stable/amd64/`

需下载以下四个安装包：

```
containerd.io
docker-ce-cli
docker-ce
docker-compose-plugin
```

示例：

```
containerd.io_2.2.1-1~debian.12~bookworm_amd64.deb
docker-ce-cli_29.2.1-1~debian.12~bookworm_amd64.deb
docker-ce_29.2.1-1~debian.12~bookworm_amd64.deb
docker-compose-plugin_5.0.2-1~debian.12~bookworm_amd64.deb
```

## 二、在内网计算机中安装

将上述四个安装包全部拷贝到内网计算机中，在安装包所在目录中执行命令：

```shell
sudo dpkg -i containerd.io_2.2.1-1~debian.12~bookworm_amd64.deb

sudo dpkg -i docker-ce-cli_29.2.1-1~debian.12~bookworm_amd64.deb

sudo dpkg -i docker-ce_29.2.1-1~debian.12~bookworm_amd64.deb

sudo dpkg -i docker-compose-plugin_5.0.2-1~debian.12~bookworm_amd64.deb

# 验证 docker 安装
docker -v
# 验证 docker compose 安装
docker compose -v
```

------

欢迎同为国网一线班组的同僚们来信交流：[official@j1net.com](mailto:official@j1net.com)