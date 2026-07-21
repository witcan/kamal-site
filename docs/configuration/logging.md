---
# This file has been generated from the Kamal source, do not edit directly.
# Find the source of this file at lib/kamal/configuration/docs/logging.yml in the Kamal repository.
title: 自定义日志配置
---

# 自定义日志配置

通过这些选项控制 Docker 的日志驱动及其参数。

## [日志设置](#logging-settings)

写在配置文件的 `logging` 键下。

可以在根级别指定，也可以针对某个角色指定。

```yaml
logging:
```

## [驱动](#driver)

要使用的日志驱动，会通过 `--log-driver` 传给 Docker：

```yaml
  driver: json-file
```

## [选项](#options)

传给驱动的日志选项，会通过 `--log-opt` 传给 Docker：

```yaml
  options:
    max-size: 100m
```
