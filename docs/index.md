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
      text: Cloudreve 官网
      link: https://cloudreve.org
    - theme: brand
      text: RustFS 官网
      link: https://rustfs.com.cn
    - theme: alt
      text: 快速开始
      link: /quick_start/

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
    details: 借助 Tika 等应用，网盘
  - icon: 🌀
    title: 方便小白部署
    details: 配合 Cloudreve、DzzOffice、Seafile 等可在内网环境中实现在线协作，作者想尝试 Office Online Server 但能力有限
  - icon: 📩
    title: 内网收取资源
    details: 配合 Cloudreve、DzzOffice、Seafile 等可在内网环境中实现在线协作，作者想尝试 Office Online Server 但能力有限
---

