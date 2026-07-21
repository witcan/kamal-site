---
title: 配置变更
---

# Kamal 2：配置变更

## [构建器](#builder)

[构建器配置](../../configuration/builders) 已简化。

### 架构

必须指定要构建的架构：

```yaml
# 单架构
builder:
  arch: amd64

# 多架构
builder:
  arch:
    - amd64
    - arm64
```

### 远程构建器

通过 remote 选项直接设置远程构建器。默认仅在构建架构与本机不匹配时使用：

```yaml
builder:
  arch: amd64
  remote: ssh://docker@docker-builder
```

可设置 `local: false` 强制 Kamal 只使用远程构建器：

```yaml
builder:
  arch: amd64
  local: false
  remote: ssh://docker@docker-builder
```

### 驱动

现在 Kamal 默认始终使用 Docker container 驱动。可自行设置 driver 来更改：

```yaml
builder:
  driver: docker
```

Docker 驱动能力有限——不支持构建缓存或多架构镜像。

## [Traefik → 代理](#traefik-to-proxy)

`traefik` 配置不再有效。改为在 [proxy](../../configuration/proxy) 下配置 kamal-proxy。

若使用了自定义 Traefik 标签或参数，请查看代理配置，确认是否可以转换。

请注意：默认情况下 kamal-proxy 将流量转发到容器的 80 端口，因为我们假定容器运行 Thruster，并监听 80 端口。若运行的是其他服务或端口，可配置 app_port：

```yaml
proxy:
  app_port: 3000
```

kamal-proxy 支持缓冲、最大请求/响应大小、转发头等常见需求，但并不涵盖 Traefik 的全部能力。

若缺少你需要的功能，可以提 issue，我们会评估；但不保证支持一切——你可能需要在栈中其他位置运行 Traefik 或其他代理来实现目标。

## [健康检查](#healthchecks)

healthcheck 配置段已移除。

### 使用代理的角色

对运行代理的角色，健康检查由 kamal-proxy 从外部执行，而非通过内部 Docker 健康检查。可在 [proxy/healthcheck](../../configuration/proxy#healthcheck) 下配置。

```yaml
proxy:
  healthcheck:
    path: /health
    interval: 2
    timeout: 2
```

请注意：健康检查会使用 `app_port` 设置，默认为 80 端口。以前健康检查默认是 3000 端口。可这样改回：

```yaml
proxy:
  app_port: 3000
```

### 不使用代理的角色

对不运行代理的角色，可通过 [options](../../configuration/roles#custom-role-configuration) 设置自定义 Docker 健康检查。

```yaml
servers:
  web:
    ...
  jobs:
    options:
      health-cmd: bin/jobs-healthy
```

对这些容器，若有健康检查，Kamal 会等待 `healthy` 状态；若没有，则等待 `running`。

可设置 `readiness_delay`：在看到 `running` 状态时使用。我们会等待该时长，并确认容器仍在运行后再继续。

### 所有角色

可在配置根级设置两个超时，适用于所有角色，无论是否使用代理。

```yaml
# 等待新容器启动的时长
deploy_timeout: 20

# 停止旧容器前等待请求完成的时长
# 取代 stop_wait_time
drain_timeout: 20

# 对无健康检查的「非代理角色」容器，等待其保持 running 状态的时长
readiness_delay: 10
```
