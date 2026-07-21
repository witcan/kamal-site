---
title: 锚点
---

# 锚点

你可以把 Kamal 配置中的部分内容定义为锚点（anchor），再通过别名（alias）引用，从而复用配置。

例如，你可能需要为多个 worker 角色定义共享的健康检查。锚点以 `x-` 开头，定义在 `deploy.yml` 的根级别。

```yaml
x-worker-healthcheck: &worker-healthcheck
  health-cmd: bin/worker-healthcheck
  health-start-period: 5s
  health-retries: 5
  health-interval: 5s
```

要在部署配置中使用该锚点，通过别名引用即可：

```yaml
servers:
  worker:
    hosts:
      - 867.53.0.9
    cmd: bin/jobs
    options:
      <<: *worker-healthcheck
```
