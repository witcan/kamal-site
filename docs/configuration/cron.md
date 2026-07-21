---
title: 定时任务
---

# 定时任务

你可以使用专门的容器来运行 Cron 任务：

```yaml
servers:
  cron:
    hosts:
      - 192.168.0.1
    cmd:
      bash -c "(env && cat config/crontab) | crontab - && cron -f"
```

该示例假定 Cron 配置保存在 `config/crontab` 中。Cron 不会自动继承环境变量，上面的例子会把它们复制进 crontab。
