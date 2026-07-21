---
# This file has been generated from the Kamal source, do not edit directly.
# Find the source of this file at lib/kamal/configuration/docs/sshkit.yml in the Kamal repository.
title: SSHKit
---

# SSHKit

[SSHKit](https://github.com/capistrano/sshkit) 是 Kamal 使用的 SSH 工具库。

默认设置对大多数场景已足够；连接大量主机时可能需要调整。

## [SSHKit 选项](#sshkit-options)

这些选项写在配置文件的 `sshkit` 键下。

```yaml
sshkit:
```

## [最大并发启动数](#max-concurrent-starts)

并发建立大量 SSH 连接时可能出问题。默认情况下，Kamal 会将并发连接启动限制为一次最多 30 个。

```yaml
  max_concurrent_starts: 10
```

## [连接池空闲超时](#pool-idle-timeout)

Kamal 将连接空闲超时设为较长的 900 秒，尽量避免在空闲一段时间后（例如构建镜像或等待 CI）出现重连风暴。

```yaml
  pool_idle_timeout: 300
```

## [DNS 重试设置](#dns-retry-settings)

某些解析器（mDNSResponder、systemd-resolved、Tailscale）在大量并发 SSH 启动时可能丢弃查询。Kamal 会自动重试 DNS 失败。

该值为首次尝试之后的重试次数。设为 0 可禁用。

```yaml
  dns_retries: 3
```
