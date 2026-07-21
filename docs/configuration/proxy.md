---
# This file has been generated from the Kamal source, do not edit directly.
# Find the source of this file at lib/kamal/configuration/docs/proxy.yml in the Kamal repository.
title: 代理
---

# 代理

Kamal 使用 [kamal-proxy](https://github.com/basecamp/kamal-proxy) 提供
无缝部署。它监听 80 和 443 端口，并将请求转发到
应用容器。

代理在根配置的 `proxy` 下配置。这些选项
在部署应用时生效，而不是在启动代理时。

它们是应用专属的，因此同一代理上运行多个应用时
不会共享这些选项。


```yaml
proxy:
```

## [主机](#hosts)

用于对外提供应用的主机名。代理只会把发往这些主机的请求
路由到你的应用。

若未设置主机，则会转发所有请求，但同服务器上其他已设置主机的
应用的匹配请求除外。

指定 `host` 或 `hosts` 之一。

```yaml
  host: foo.example.com
  hosts:
    - foo.example.com
    - bar.example.com
```

## [应用端口](#app-port)

应用容器暴露的端口。

默认为 80：

```yaml
  app_port: 3000
```

## [SSL](#ssl)

kamal-proxy 可通过 Let's Encrypt 为应用提供自动 HTTPS。

这要求只部署到一台服务器，且已设置 host 选项。
host 的值必须指向正在部署的那台服务器，且 443 端口必须开放，
以便 Let's Encrypt 验证成功。

若将 `ssl` 设为 `true`，`kamal-proxy` 将停止向应用转发头信息，
除非你显式设置 `forward_headers: true`。

默认为 `false`：

```yaml
  ssl: true
```

## [自定义 SSL 证书](#custom-ssl-certificate)

某些情况下，使用 Let's Encrypt 自动管理证书不可行，
例如部署在多台主机上时。

或者你可能已有由其他证书颁发机构（CA）签发的 SSL 证书。

Kamal 支持直接从密钥加载自定义 SSL 证书。你应传入一个哈希，
将 `certificate_pem` 和 `private_key_pem` 映射到密钥名称。

```yaml
  ssl:
    certificate_pem: CERTIFICATE_PEM
    private_key_pem: PRIVATE_KEY_PEM
```

### 说明
- 若证书或密钥缺失或无效，部署将失败。
- 请始终安全处理 SSL 证书和私钥，避免硬编码进版本控制。

## [SSL 重定向](#ssl-redirect)

默认情况下，启用 SSL 时 kamal-proxy 会将所有 HTTP 请求重定向到 HTTPS。
若希望 HTTP 流量也直接转发到应用（与 HTTPS 一样），
可设置 `ssl_redirect: false` 禁用重定向：

```yaml
  ssl_redirect: false
```

## [转发头](#forward-headers)

是否转发 `X-Forwarded-For` 和 `X-Forwarded-Proto` 头。

若你位于受信任的代理之后，可设为 `true` 以转发这些头。

默认情况下，若 `ssl` 为 `true`，kamal-proxy 不会转发这些头；
若 `ssl` 为 `false`，则会转发。

```yaml
  forward_headers: true
```

## [响应超时](#response-timeout)

等待请求完成的超时时间，默认 30 秒：

```yaml
  response_timeout: 10
```

## [基于路径的路由](#path-based-routing)

若应用根据请求路径把流量分到不同服务，
可使用基于路径的路由，将服务挂载在不同路径前缀下。
用法示例：path_prefix: '/api'

也可以用两种方式指定多个路径。

使用 path_prefix 时，可用逗号分隔多条路由。

```yaml
  path_prefix: "/api,/oauth_callback"
```

也可以把路径写成列表，配置会被合并成逗号分隔的字符串。

```yaml
  path_prefixes:
    - "/api"
    - "/oauth_callback"
```

默认情况下，转发到上游前会去掉路径前缀。

因此在上面的例子中，对 /api/users/123 的请求会以 /users/123 转发给 web-1。

若希望按原始路径（含前缀）转发，
请指定 --strip-path-prefix=false：

```yaml
  strip_path_prefix: false
```

## [健康检查](#healthcheck)

部署时，代理默认每秒访问一次 `/up`，直到达到部署超时；
每次请求超时为 5 秒。

应用就绪后，代理会停止访问健康检查端点。

```yaml
  healthcheck:
    interval: 3
    path: /health
    timeout: 3
```

## [缓冲](#buffering)

是否在代理中缓冲请求和响应体。

默认启用缓冲，最大请求体为 1GB，响应体无限制。

还可以设置缓冲的内存上限，默认为 1MB；超过该大小的内容会写入磁盘。

```yaml
  buffering:
    requests: true
    responses: true
    max_request_body: 40_000_000
    max_response_body: 0
    memory: 2_000_000
```

## [日志](#logging)

配置代理的请求日志。
可指定要记录的请求头和响应头。
默认会记录 `Cache-Control`、`Last-Modified` 和 `User-Agent` 请求头：

```yaml
  logging:
    request_headers:
      - Cache-Control
      - X-Forwarded-Proto
    response_headers:
      - X-Request-ID
      - X-Request-Start
```

## [运行配置](#run-configuration)

这些选项在启动代理容器时使用。


```yaml
  run:
    http_port: 8080                # 使用的 HTTP 端口（默认 80）
    https_port: 8443               # 使用的 HTTPS 端口（默认 443）
    metrics_port: 9090             # Prometheus 指标端口
    debug: true                    # 调试日志（默认：false）
    log_max_size: "30m"            # 最大日志文件大小（默认："10m"）
    publish: false                 # 是否将端口发布到主机（默认：true）
    bind_ips:                      # 发布端口时绑定的 IP 列表
      - 0.0.0.0
    registry: registry:4443        # kamal-proxy 镜像的容器仓库
                                   # （默认为 Docker Hub）
    repository: myrepo/kamal-proxy # kamal-proxy 镜像的仓库名
                                   # （默认为 `basecamp/kamal-proxy`）
    version: v0.8.0                # 使用的 kamal-proxy 镜像版本标签
    options:                       # 传给 `docker run` 的额外选项
      label:
        - custom.label=kamal-proxy
      memory: 512m
      cpus: 0.5
```

## [在角色上启用/禁用代理](#enabling/disabling-the-proxy-on-roles)

默认在主角色上启用代理；可在主角色配置中设置
`proxy: false` 禁用。

```yaml
servers:
  web:
    hosts:
     - ...
    proxy: false
```

默认在其他所有角色上禁用；可设置 `proxy: true`，
或为该角色提供 proxy 配置以启用。

```yaml
servers:
  web:
    hosts:
     - ...
  web2:
    hosts:
     - ...
    proxy: true
```
