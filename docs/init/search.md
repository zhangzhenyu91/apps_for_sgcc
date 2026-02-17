# 初始化 -2- 开启全文搜索

## 一、示例

- 开启全文搜索，在搜索栏中搜索文件时，会一并搜索支持文档格式中的文字内容，如图搜索“吕胜”，会搜索出文件名**没有**“吕胜”但**内容包含**“吕胜”的文档：

![示例](/search/example.webp)

## 二、开启步骤

### Meilisearch 参数设置

![Meili参数](/search/search.webp)

- 进入管理后台 - **①文件系统** - **②全文搜索**，开启 **③启用全文搜索**；
- 在 **④Meilisearch 端点** 中输入跟 Cloudreve 一起部署的 Meilisearch 地址，若是按此页文档部署的话，您可以查看 docker-compose.yml 文件中 Meilisearch 对应的端口号，再在前面加上宿主机的 IP 地址，如下示例若宿主机 IP 为 ´10.2.24.13´ 则端点应填写为：`http://10.2.24.13:7700`：

```yaml
  meilisearch:
    image: getmeili/meilisearch:v1.35.0
    container_name: meilisearch
    restart: always
    ports:
      # 下面一行":"前的数字即为 Meilisearch 的端口号
      - 7700:7700
    environment:
      # 下面一行"="后单引号中的字符串即为 ⑤API 密钥
      - MEILI_MASTER_KEY='TmDSKZhcoEMzTt3STPhRQVRxPEVZw7m2TymKwKhs_YaL3GF'
    volumes:
      - meili_data:/meili_data

```

- 在 **⑤API 密钥** 中填入 Meilisearch 的密钥，若按此页文档部署则密钥即为上方代码中 "MEILI_MASTER_KEY=" 后单引号中的字符串（替换为你自己的）；

### Tika 参数设置

```yaml
  tika:
    image: apache/tika:latest
    container_name: tika
    restart: always
    ports:
      # 下面一行":"前的数字即为 Tika 的端口号
      - 9998:9998
```

若按此页文档部署的 Cloudreve，则可在 docker-compose.yml 中查看 Tika 服务的端口号如上述代码，配合本机 IP 地址将 `http://10.2.24.13:9998` 填写在 **Tika 端点** 中：

![Tika参数](/search/tika.webp)

### 重建索引

启用全文搜索后，Cloudreve 不会自动为已存在的文件建立索引，所以需要在上述设置完成后点击 **保存**，再点击 **重建索引**。

![重建索引](/search/reconstruct.webp)

***

欢迎同为国网一线班组的同僚们来信交流：[badzhang@j1net.org](mailto:badzhang@j1net.org)