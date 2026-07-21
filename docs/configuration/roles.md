---
# This file has been generated from the Kamal source, do not edit directly.
# Find the source of this file at lib/kamal/configuration/docs/role.yml in the Kamal repository.
title: 角色
---

# 角色

角色用于配置部署中不同类型的服务器。
最常见的用途是区分 Web 服务器和任务（job）服务器。

除非在根配置中设置了不同的 `primary_role`，Kamal 默认期望存在 `web` 角色。

## [角色配置](#role-configuration)

角色写在 `servers` 键下：

```yaml
servers:
```

## [简单角色配置](#simple-role-configuration)

若不需要为角色做自定义配置，可以直接写成主机列表。

你可以在主机上设置标签以使用自定义环境变量（参见[环境变量](../environment-variables)）：

```yaml
  web:
    - 172.1.0.1
    - 172.1.0.2: experiment1
    - 172.1.0.2: [ experiment1, experiment2 ]
```

## [自定义角色配置](#custom-role-configuration)

当还有其他选项需要设置时，主机列表写在 `hosts` 键下。

默认只有主角色（primary role）会使用代理。

对其他角色，可设置 `proxy: true` 启用代理并继承根级 proxy 配置，或提供选项映射以覆盖根配置。

对主角色，可设置 `proxy: false` 禁用代理。

你也可以设置自定义的 `cmd` 在容器中运行，并覆盖根配置中的其他设置。

```yaml
  workers:
    hosts:
      - 172.1.0.3
      - 172.1.0.4: experiment1
    cmd: "bin/jobs"
    stop_timeout: 30
    options:
      memory: 2g
      cpus: 4
    logging:
      ...
    proxy:
      ...
    labels:
      my-label: workers
    env:
      ...
    asset_path: /public
```
