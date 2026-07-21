---
title: kamal rollback
---

# kamal rollback

可用 `kamal rollback` 回滚一次部署。

若发现某次部署有问题，可以快速回滚到先前的镜像。运行 `kamal app containers -q` 可查看可用于回滚的旧容器。输出形式与 `kamal app details` 类似，但会包含所有旧容器。

```
App Host: 192.168.0.1
CONTAINER ID   IMAGE                                                                         COMMAND                    CREATED          STATUS                      PORTS      NAMES
1d3c91ed1f51   registry.digitalocean.com/user/app:6ef8a6a84c525b123c5245345a8483f86d05a123   "/rails/bin/docker-e..."   19 minutes ago   Up 19 minutes               3000/tcp   chat-6ef8a6a84c525b123c5245345a8483f86d05a123
539f26b28369   registry.digitalocean.com/user/app:e5d9d7c2b898289dfbc5f7f1334140d984eedae4   "/rails/bin/docker-e..."   31 minutes ago   Exited (1) 27 minutes ago              chat-e5d9d7c2b898289dfbc5f7f1334140d984eedae4

App Host: 192.168.0.2
CONTAINER ID   IMAGE                                                                         COMMAND                    CREATED          STATUS                      PORTS      NAMES
badb1aa51db4   registry.digitalocean.com/user/app:6ef8a6a84c525b123c5245345a8483f86d05a123   "/rails/bin/docker-e..."   19 minutes ago   Up 19 minutes               3000/tcp   chat-6ef8a6a84c525b123c5245345a8483f86d05a123
6f170d1172ae   registry.digitalocean.com/user/app:e5d9d7c2b898289dfbc5f7f1334140d984eedae4   "/rails/bin/docker-e..."   31 minutes ago   Exited (1) 27 minutes ago              chat-e5d9d7c2b898289dfbc5f7f1334140d984eedae4
```

在上面的例子中，`e5d9d7c2b898289dfbc5f7f1334140d984eedae4` 是上一个版本，因此可作为回滚目标。运行 `kamal rollback e5d9d7c2b898289dfbc5f7f1334140d984eedae4` 即可执行回滚。

这会停止 `6ef8a6a84c525b123c5245345a8483f86d05a123`，然后启动一个使用与 `e5d9d7c2b898289dfbc5f7f1334140d984eedae4` 相同镜像的新容器。无需从镜像仓库重新下载。

**注意：** 默认情况下，运行 `kamal deploy` 时会清理 3 天前的旧容器。
