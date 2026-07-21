---
# This file has been generated from the Kamal source, do not edit directly.
# Find the source of this file at lib/kamal/configuration/docs/registry.yml in the Kamal repository.
title: Docker 镜像仓库
---

# 镜像仓库

默认镜像仓库是 Docker Hub，你可以通过 `registry/server` 修改。

## [使用本地容器镜像仓库](#using-a-local-container-registry)

如果 registry 的 server 以 `localhost` 开头，Kamal 会在该端口启动本地 Docker 镜像仓库，并将应用镜像推送到那里。

```yaml
registry:
  server: localhost:5555
```

## [使用 Docker Hub 作为容器镜像仓库](#using-docker-hub-as-the-container-registry)

默认情况下，Docker Hub 会创建公开仓库。为避免镜像被公开，请在部署前创建私有仓库，或在 [Docker Hub 设置](https://hub.docker.com/repository-settings/default-privacy) 中将默认仓库可见性改为私有。

对密钥的引用（本例中为 `KAMAL_REGISTRY_PASSWORD`）会从本地环境中查找该密钥：

```yaml
registry:
  username:
    - <your docker hub username>
  password:
    - KAMAL_REGISTRY_PASSWORD
```

## [使用 AWS ECR 作为容器镜像仓库](#using-aws-ecr-as-the-container-registry)

本地需要安装 AWS CLI 才能使用。
AWS ECR 的访问令牌仅 12 小时有效。为避免每次手动重新生成令牌，可在 `deploy.yml` 中使用 ERB，通过 AWS CLI 命令获取令牌：

```yaml
registry:
  server: <your aws account id>.dkr.ecr.<your aws region id>.amazonaws.com
  username: AWS
  password: <%= %x(aws ecr get-login-password) %>
```

## [使用 GCP Artifact Registry 作为容器镜像仓库](#using-gcp-artifact-registry-as-the-container-registry)

要登录 Artifact Registry，需要
[创建服务账号](https://cloud.google.com/iam/docs/service-accounts-create#creating)
并[配置角色与权限](https://cloud.google.com/artifact-registry/docs/access-control#permissions)。
通常分配 `roles/artifactregistry.writer` 角色即可。

服务账号就绪后，需要生成并下载 JSON 密钥，再进行 base64 编码：

```shell
base64 -i /path/to/key.json | tr -d "\\n"
```

然后将 `KAMAL_REGISTRY_PASSWORD` 密钥设为该值。

使用该环境变量作为密码，用户名使用 `_json_key_base64`。最终配置如下：

```yaml
registry:
  server: <your registry region>-docker.pkg.dev
  username: _json_key_base64
  password:
    - KAMAL_REGISTRY_PASSWORD
```

## [验证配置](#validating-the-configuration)

可通过运行以下命令验证配置：

```shell
kamal registry login
```
