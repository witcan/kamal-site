---
title: Prune
---

# kamal prune

清理旧容器和镜像。

Kamal 会保留最近 5 次部署的容器及其使用的镜像。清理会删除所有更旧的容器和镜像。

```bash
$ kamal prune
Commands:
  kamal prune all             # 清理未使用的镜像和已停止的容器
  kamal prune containers      # 清理所有已停止的容器，保留最近 n 个（默认 5）
  kamal prune help [COMMAND]  # 说明子命令或某个具体子命令
  kamal prune images          # 清理未使用的镜像
```
