---
# This file has been generated from the Kamal source, do not edit directly.
# Find the source of this file at lib/kamal/configuration/docs/servers.yml in the Kamal repository.
title: 服务器
---

# 服务器

服务器按不同角色划分，每个角色可以有各自的配置。

对于更简单的部署——所有服务器都相同——你也可以直接写一份服务器列表。它们会隐式归入 `web` 角色。

```yaml
servers:
  - 172.0.0.1
  - 172.0.0.2
  - 172.0.0.3
```

## [为服务器打标签](#tagging-servers)

服务器可以打标签，标签用于添加自定义环境变量（参见[环境变量](../environment-variables)）。

```yaml
servers:
  - 172.0.0.1
  - 172.0.0.2: experiments
  - 172.0.0.3: [ experiments, three ]
```

## [角色](#roles)

对于更复杂的部署（例如还要跑任务主机），可以为角色分别配置（参见[角色](../roles)）：

```yaml
servers:
  web:
    ...
  workers:
    ...
```
