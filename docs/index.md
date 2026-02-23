---
# https://vitepress.dev/reference/default-theme-home-page
layout: home

hero:
  name: "国网一线应用探索"
  text: "App Exploration<br>For SGCC"
  tagline: "一线班组员工探索可用、好用的软件项目，欢迎交流"
  image:
    src: /logo.svg
    alt: 背景图
  actions:
    - theme: brand
      text: 快速开始
      link: /quick_start/
    - theme: alt
      text: 可道云官网
      link: https://kodcloud.com
    - theme: alt
      text: Cloudreve 官网
      link: https://cloudreve.org
    - theme: alt
      text: RustFS 官网
      link: https://rustfs.com.cn
features:
  - icon: 💾
    title: 存储硬件整合
    details: 在每台计算机中单独部署 RustFS，在性能较强的计算机中部署网盘服务，利用 S3 协议连接，整合硬盘资源，实现任一计算机均可访问全部资源
  - icon: 📝
    title: 在线协同编辑
    details: 通过 OnlyOffice（中国版）与网盘集成，实现内网环境中的在线文档协作，无需二次整合，直接在同一文档中修改
  - icon: 📤
    title: 高效文件收发
    details: 传统大文件发送依托非局域网中的邮件服务器，上传、下载非常耗时，利用网盘分享链接实现局域网内传输，速度大大提升
  - icon: 🔍
    title: 文档内容搜索
    details: 借助 Tika 等应用，可实现搜索时连带文档内容一起搜索，在忘记文件名的情况下也可根据文档内容搜索文件
  - icon: 🌀
    title: 方便小白部署
    details: 文档编写基本涵盖每一步，无需理解，按步骤执行即可完成安装部署<br>演示测试：银河麒麟 V10 SP1 2503
  - icon: 📩
    title: 内网收取资源
    details: 文档中包含的镜像tar包、docker-compose.yml示例文件均可联系作者获取，支持国网内网邮件发送<br>联系邮箱：badzhang@j1net.org
---