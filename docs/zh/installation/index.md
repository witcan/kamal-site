---
title: 安装
lang: zh
nav: false
---

# 安装 Kamal

如果你已有 Ruby 环境，可以全局安装 Kamal：

```sh
gem install kamal
```

如果尚未安装 Ruby，也可以[在 Docker 容器中运行 Kamal](/docs/installation/dockerized/)，不过会有一些限制。

然后在应用目录中运行 `kamal init`，再编辑新生成的 `config/deploy.yml`。内容可以非常简洁，例如：

```yaml
service: hey
image: 37s/hey
servers:
  - 192.168.0.1
  - 192.168.0.2
registry:
  username: registry-user-name
  password:
    - KAMAL_REGISTRY_PASSWORD
builder:
  arch: amd64
env:
  secret:
    - RAILS_MASTER_KEY
```

在环境中设置 `KAMAL_REGISTRY_PASSWORD`，并编辑 `.kamal/secrets` 文件以读取它（若是 Rails 应用的生产环境，还需读取 `RAILS_MASTER_KEY`）：

```yaml
KAMAL_REGISTRY_PASSWORD=$KAMAL_REGISTRY_PASSWORD
RAILS_MASTER_KEY=$(cat config/master.key)
```

现在可以部署到服务器了：

```
kamal setup
```

该命令会：

1. 通过 SSH 连接服务器（默认使用 root，由你的 SSH 密钥认证）。
2. 在尚未安装 Docker 的服务器上安装 Docker（使用 get.docker.com）：需要 root 的 SSH 权限。
3. 在本地和远程登录镜像仓库。
4. 使用应用根目录的标准 Dockerfile 构建镜像。
5. 将镜像推送到镜像仓库。
6. 从镜像仓库将镜像拉取到服务器。
7. 确保 kamal-proxy 已运行，并在 80、443 端口接受流量。
8. 用与当前 Git 版本哈希匹配的应用版本启动新容器。
9. 当新容器对 `GET /up` 返回 `200 OK` 后，让 kamal-proxy 将流量路由到新容器。
10. 停止运行旧版本应用的旧容器。
11. 清理未使用的镜像和已停止的容器，避免服务器磁盘被占满。

完成！所有服务器现在都在 80 端口提供应用服务。如果只有一台服务器，已经可以直接使用。如果有多台服务器，需要在前面放一个负载均衡器。后续部署，或服务器上已安装 Docker 时，直接运行 `kamal deploy` 即可。
