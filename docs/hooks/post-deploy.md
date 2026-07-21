---
title: post-deploy
---

# 钩子：post-deploy

在 deploy、redeploy 或 rollback 之后运行。此钩子还会收到 `KAMAL_RUNTIME` 环境变量，值为本次部署总耗时（秒）。

可用于广播部署消息，或向 APM 注册新版本。

命令示例：

```bash
#!/usr/bin/env bash
curl -q -d content="[My App] ${KAMAL_PERFORMER} Rolled back to version ${KAMAL_VERSION}" https://3.basecamp.com/XXXXX/integrations/XXXXX/buckets/XXXXX/chats/XXXXX/lines
```

这会向 Basecamp 中预配置的聊天机器人发送类似下面的一行：

```
[My App] [dhh] Rolled back to version d264c4e92470ad1bd18590f04466787262f605de
```
