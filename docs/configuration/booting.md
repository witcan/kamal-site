---
# This file has been generated from the Kamal source, do not edit directly.
# Find the source of this file at lib/kamal/configuration/docs/boot.yml in the Kamal repository.
title: 启动
---

# 启动

部署到大量主机时，你可能不希望在所有主机上同时重启服务。

Kamal 默认会在所有主机上并行启动新容器。你也可以通过 boot 配置控制这一行为。


```yaml
boot:
```

## [每次启动的主机数量或比例](#the-number-or-percentage-of-hosts-to-boot-at-a-time.)

可以是整数（例如 3），也可以是百分比字符串（例如 25%）。

```yaml
  limit: 25%
```

## [每组主机启动之间的等待秒数](#the-number-of-seconds-to-wait-between-booting-each-group-of-hosts.)

```yaml
  wait: 10
```

## [是否在同一主机上并行启动角色](#whether-to-boot-roles-in-parallel-on-a-host.)

如果一台主机有多个角色，可控制这些角色在该主机上是并行启动还是顺序启动。

默认为 false。

```yaml
  parallel_roles: true
```
