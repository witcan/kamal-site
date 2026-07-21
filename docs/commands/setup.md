---
title: Setup
---

# kamal setup

Kamal setup 会在全新主机上执行部署应用所需的全部步骤。

它会：

1. 若有权限且尚未安装，则在所有服务器上安装 Docker。
2. 启动全部附属服务。
3. 部署应用（见 [`kamal deploy`](../deploy)）。
