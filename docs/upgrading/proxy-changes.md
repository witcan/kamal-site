---
title: 代理变更
---

# Kamal 2：代理变更

在 Kamal 1 中，我们使用 [Traefik](https://traefik.io/traefik) 实现无缝部署。

从第 2 版起，我们改用专为 Kamal 打造的自定义代理 [kamal-proxy](https://github.com/basecamp/kamal-proxy)。

## [为什么弃用 Traefik](#dropping-traefik)

### 命令式 vs. 声明式

Traefik 与 Kamal 并不契合。Kamal 是命令式工具，而 Traefik 是声明式的。

这意味着我们需要请求 Traefik 做事，再轮询它是否完成。

### 标签

我们使用了 Traefik 的 Docker provider。它要求给容器加标签，Traefik 再据此配置自己。

这种方式很灵活，标签能做的事情很多。但即便完成简单任务，也需要理解并使用 Traefik 的[通用配置](https://doc.traefik.io/traefik/providers/docker/)。

容器标签不可变，因此无法让 Traefik 停止路由请求。为成功排空容器，我们不得不修改健康检查，强制容器进入不健康状态。

### 过于灵活

通过 Traefik 标签，有可能让 Kamal 做它最初未设计的事情，例如集成 Let's Encrypt，或在一台服务器上运行多个应用。

这些用法不受支持且容易出错，我们希望为这些常见需求提供更简单的内置方案。

### 错误难以理解

Traefik 有自己的领域语言——Routers、Services、EntryPoints。一旦失败，错误信息用的是那套语言，与 Kamal 正在做的事脱节，排查起来很困难。

### 其他选项

还有其他代理可选，Traefik 也有文件 provider 等配置方式。

但为了演进 Kamal，很明显自建代理才能获得高效构建与开发新功能所需的控制力。

我们需要：

- 命令式代理——即无需轮询
- Kamal 命令与代理命令一一对应
- 清晰的错误信息
- 支持新命令
- 配置在部署时而非启动时生效，从而无需重启即可修改

要达成这些目标，显然需要自建代理。

## [kamal-proxy](#kamal-proxy)

[kamal-proxy](https://github.com/basecamp/kamal-proxy) 用 Go 编写。

配置尽量精简，以便多个应用共用一个代理而不会冲突。

配置（超时、日志、缓冲等）在部署时通过命令提供，且只作用于正在部署的应用。

它使用阻塞命令，因此部署时命令会在部署完成后再返回。

它支持：

- 通过 Let's Encrypt 自动 TLS
- 基于主机的路由
- 请求与响应缓冲
- 最大请求与响应大小

即将在 Kamal 中提供：

- 暂停请求
- 维护模式
- 渐进式发布

代理以容器形式通过 [Docker Hub](https://hub.docker.com/repository/docker/basecamp/kamal-proxy) 分发。

Kamal 会在部署前确保兼容版本已就绪。
