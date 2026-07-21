---
title: 通过 Docker 运行 Kamal
---

# 通过 Docker 运行 Kamal

在 macOS 上使用：

```sh
alias kamal='docker run -it --rm -v "${PWD}:/workdir" -v "/run/host-services/ssh-auth.sock:/run/host-services/ssh-auth.sock" -e SSH_AUTH_SOCK="/run/host-services/ssh-auth.sock" -v /var/run/docker.sock:/var/run/docker.sock ghcr.io/basecamp/kamal:latest'
```

在 Linux 上使用：

```sh
alias kamal='docker run -it --rm -v "${PWD}:/workdir" -v "${SSH_AUTH_SOCK}:/ssh-agent" -v /var/run/docker.sock:/var/run/docker.sock -e "SSH_AUTH_SOCK=/ssh-agent" ghcr.io/basecamp/kamal:latest'
```

## 限制

使用上述 Docker alias 时，Kamal 命令在容器内执行，而不是直接在宿主机上运行，因此存在一些限制。

若要避免这些限制，请[通过 Ruby 安装 Kamal](..)。

### 仅转发 SSH Agent

该 alias 会把 SSH agent 转发进容器，而不会注入你的私钥。如果需要在容器内使用完整的 SSH 配置，可以加上 `-v "$HOME/.ssh:/root/.ssh"`，但请注意这会把私钥暴露给容器。

### 密钥（Secrets）

你将无法使用 Kamal 的 secret 适配器，因为密钥管理工具的命令行程序在容器内不可用。

### 环境变量

宿主机上的环境变量默认不可用，除非你修改命令，例如通过 `-e KAMAL_REGISTRY_PASSWORD=$KAMAL_REGISTRY_PASSWORD` 注入它们。
