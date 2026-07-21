---
title: pre-app-boot
---

# 钩子：pre-app-boot

在调用 `kamal app boot`（或间接通过 `kamal deploy`）启动应用容器之前运行。

若使用分组启动策略，该钩子会对每个组调用一次，此时 `KAMAL_HOSTS` 包含该组内的服务器列表。

启动完成后会调用 [post-app-boot](../post-app-boot)，同样按每个部署组调用一次。
