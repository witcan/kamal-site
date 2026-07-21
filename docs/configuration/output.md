---
# This file has been generated from the Kamal source, do not edit directly.
# Find the source of this file at lib/kamal/configuration/docs/output.yml in the Kamal repository.
title: 输出
---

# 输出

配置 Kamal 将命令输出日志发送到何处。

## [输出选项](#output-options)

这些选项写在配置文件的 `output` 键下。

```yaml
output:
```

## [OTel](#otel)

通过 OTLP HTTP 将部署日志发送到兼容 OpenTelemetry 的端点。

日志以 OTLP 日志记录发送，资源属性来自 Kamal 的部署标签（service、version、performer、destination 等）。

```yaml
  otel:
    endpoint: http://otel-gateway:4318
```

## [文件](#file)

将部署日志写入本机文件。

每次部署会创建一个日志文件，文件名包含时间戳和命令名。

```yaml
  file:
    path: /var/log/kamal/
```
