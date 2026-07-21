---
title: 构建器示例
---

# 构建器示例

## [单架构使用远程构建器](#using-remote-builder-for-single-arch)

如果你在 ARM64（例如 Apple Silicon）上开发，但要部署到 AMD64（x86 64 位），默认情况下 Kamal 会配置本地 buildx，通过 QEMU 模拟完成构建。不过这可能相当慢，尤其是第一次构建。

若想用远程 AMD64 主机原生构建 AMD64 部分以加快速度，可以设置远程构建器：

```yaml
builder:
  arch: amd64
  remote: ssh://root@192.168.0.1
```

从 ARM64 机器部署时，Kamal 会使用远程构建；从 AMD64 机器部署时则在本地构建。

**注意：** 作为构建器的远程主机上必须运行 Docker。该实例应仅与使用相同镜像仓库和凭证的构建共享。

## [多架构使用远程构建器](#using-remote-builder-for-native-multi-arch)

你也可以构建多架构镜像。若设置了 remote，Kamal 会在本地构建与部署服务器匹配的架构，在远程构建另一架构。

因此若你在 ARM64（例如 Apple Silicon）上开发，会在本地构建 ARM64 架构，在远程构建 AMD64 架构。

```yaml
builder:
  arch:
    - amd64
    - arm64
  remote: ssh://root@192.168.0.1
```

## [单架构使用本地构建器](#using-local-builder-for-single-arch)

若始终只为单一架构本地构建，Kamal 会使用本地 buildx 实例构建镜像。

```yaml
builder:
  arch: amd64
```

## [构建时使用不同的 Dockerfile 或上下文](#using-a-different-dockerfile-or-context-when-building)

若需要向构建命令传入不同的 Dockerfile 或上下文（例如 monorepo，或有多份 Dockerfile），可在 builder 选项中设置：

```yaml
# 使用不同的 Dockerfile
builder:
  dockerfile: Dockerfile.xyz

# 设置上下文
builder:
  context: ".."

# 同时设置 Dockerfile 和上下文
builder:
  dockerfile: "../Dockerfile.xyz"
  context: ".."
```

## [使用多阶段构建缓存](#using-multistage-builder-cache)

Docker 多阶段构建缓存可以加快构建。目前 Kamal 仅支持使用 GHA 缓存或 Registry 缓存：

```yaml
# 使用 GHA 缓存
builder:
  cache:
    type: gha

# 使用 Registry 缓存
builder:
  cache:
    type: registry

# 使用 Registry 缓存并指定不同的缓存镜像
builder:
  cache:
    type: registry
    # 默认镜像名为 <image>-build-cache
    image: application-cache-image

# 使用 Registry 缓存并附加 cache-to 选项
builder:
  cache:
    type: registry
    options: mode=max,image-manifest=true,oci-mediatypes=true
```

## [在本地不使用 Dockerfile 进行构建](#building-without-a-dockerfile-locally)

应用镜像也可以用 [cloud native buildpacks](https://buildpacks.io/) 构建，而不使用 `Dockerfile` 和默认的 `docker build` 流程。下面的例子使用 Heroku 的 [ruby](https://github.com/heroku/heroku-buildpack-ruby) 和 [Procfile](https://github.com/heroku/buildpacks-procfile) buildpack 构建最终镜像。

``` yaml
  pack:
    builder: heroku/builder:24
    buildpacks:
      - heroku/ruby
      - heroku/procfile
```

如需更多自定义，可在应用根目录添加[项目描述文件](https://buildpacks.io/docs/for-app-developers/how-to/build-inputs/use-project-toml/)（`project.toml`）。

### [GHA 缓存配置](#gha-cache-configuration)

要在 GitHub Action 工作流中生效，需要设置 buildx 并暴露[缓存的认证配置](https://docs.docker.com/build/cache/backends/gha/#authentication)。

示例设置（在 .github/workflows/sample-ci.yml 中）：

```yaml
- name: Set up Docker Buildx for cache
  uses: docker/setup-buildx-action@v3

- name: Expose GitHub Runtime for cache
  uses: crazy-max/ghaction-github-runtime@v3
```

配置正确后，应能在 GHA 工作流的 actions 缓存区域看到缓存条目。

关于构建缓存优化的更多说明，见 Docker 官网文档：https://docs.docker.com/build/cache/。

## [为新镜像使用构建密钥](#using-build-secrets-for-new-images)

有些镜像在构建时需要传入密钥，例如 GITHUB_TOKEN，以访问私有 gem 仓库。可在 `.kamal/secrets` 中设置密钥，再在 builder 配置中引用：

```bash
# .kamal/secrets

GITHUB_TOKEN=$(gh config get -h github.com oauth_token)
```

```yaml
# config/deploy.yml

builder:
  secrets:
    - GITHUB_TOKEN
```

然后在 Dockerfile 中引用该构建密钥：

```dockerfile
# Copy Gemfiles
COPY Gemfile Gemfile.lock ./

# Install dependencies, including private repositories via access token (then remove bundle cache with exposed GITHUB_TOKEN)
RUN --mount=type=secret,id=GITHUB_TOKEN \
  BUNDLE_GITHUB__COM=x-access-token:$(cat /run/secrets/GITHUB_TOKEN) \
  bundle install && \
  rm -rf /usr/local/bundle/cache
```

## [为新镜像配置构建参数](#configuring-build-args-for-new-images)

非敏感的构建参数也可以配置：

```yaml
builder:
  args:
    RUBY_VERSION: 3.2.0
```

然后在 Dockerfile 中使用该构建参数：

```dockerfile
ARG RUBY_VERSION
FROM ruby:$RUBY_VERSION-slim as base
```
