---
# This file has been generated from the Kamal source, do not edit directly.
# Find the source of this file at lib/kamal/configuration/docs/builder.yml in the Kamal repository.
title: 构建器
---

# 构建器

构建器配置控制如何通过 `docker build` 构建应用。

更多信息见[构建器示例](/docs/configuration/builder-examples/)。

## [构建器选项](#builder-options)

选项写在根配置的 `builder` 键下。

```yaml
builder:
```

## [架构](#arch)

要构建的架构——可以是数组，也可以是单个值。

允许的值为 `amd64` 和 `arm64`：

```yaml
  arch:
    - amd64
```

## [远程](#remote)

远程构建器的连接字符串。若提供，Kamal 会在构建与部署主机本地架构不匹配时使用它。

```yaml
  remote: ssh://docker@docker-builder
```

## [本地](#local)

若设为 false，即使构建本地架构，Kamal 也会始终使用远程构建器。

默认为 true：

```yaml
  local: true
```

## [Buildpack 配置](#buildpack-configuration)

使用 pack 构建 Cloud Native Buildpack 镜像时的构建配置。

若需更多 buildpack 自定义选项，可创建项目描述文件（project.toml），
Pack CLI 会自动使用。详见 https://buildpacks.io/docs/for-app-developers/how-to/build-inputs/use-project-toml/。

```yaml
  pack:
    builder: heroku/builder:24
    buildpacks:
      - heroku/ruby
      - heroku/procfile
```

## [构建缓存](#builder-cache)

类型必须是 `gha` 或 `registry`。

`image` 仅用于 registry 缓存，且与 Docker 驱动不兼容：

```yaml
  cache:
    type: registry
    options: mode=max
    image: kamal-app-build-cache
```

## [构建上下文](#build-context)

若未设置，则会使用仓库的本地 Git 克隆。
这样可保证构建干净，不包含未提交的更改。

若要使用本地工作区，可将 context 设为 `.`，或设为其他目录的路径。

```yaml
  context: .
```

## [Dockerfile](#dockerfile)

用于构建的 Dockerfile，默认为 `Dockerfile`：

```yaml
  dockerfile: Dockerfile.production
```

## [构建目标](#build-target)

若未设置，则使用默认 target：

```yaml
  target: production
```

## [构建参数](#build-arguments)

额外的构建参数，会通过 `--build-arg <key>=<value>` 传给 `docker build`：

```yaml
  args:
    ENVIRONMENT: production
```

## [引用构建参数](#referencing-build-arguments)

```shell
ARG RUBY_VERSION
FROM ruby:$RUBY_VERSION-slim as base
```

## [构建密钥](#build-secrets)

值从 `.kamal/secrets` 读取：

```yaml
  secrets:
    - SECRET1
    - SECRET2
```

## [引用构建密钥](#referencing-build-secrets)

```shell
# Copy Gemfiles
COPY Gemfile Gemfile.lock ./

# Install dependencies, including private repositories via access token
# Then remove bundle cache with exposed GITHUB_TOKEN
RUN --mount=type=secret,id=GITHUB_TOKEN \
  BUNDLE_GITHUB__COM=x-access-token:$(cat /run/secrets/GITHUB_TOKEN) \
  bundle install && \
  rm -rf /usr/local/bundle/cache
```

## [SSH](#ssh)

向构建暴露的 SSH agent socket 或密钥：

```yaml
  ssh: default=$SSH_AUTH_SOCK
```

## [驱动](#driver)

使用的构建驱动，默认为 `docker-container`：

```yaml
  driver: docker
```


若要使用 Docker Build Cloud（https://www.docker.com/products/build-cloud/），可将驱动设为：

```yaml
  driver: cloud org-name/builder-name
```

## [来源证明（Provenance）](#provenance)

用于配置构建结果的 provenance 证明。
值也可以是布尔值，以启用或禁用 provenance 证明。

```yaml
  provenance: mode=max
```

## [SBOM（软件物料清单）](#sbom-(software-bill-of-materials))

用于配置构建结果的 SBOM 生成。
值也可以是布尔值，以启用或禁用 SBOM 生成。

```yaml
  sbom: true
```
