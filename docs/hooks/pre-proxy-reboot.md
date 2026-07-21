---
title: pre-proxy-reboot
---

# 钩子：pre-proxy-reboot

在调用 `kamal proxy reboot` 重启 kamal-proxy 容器之前运行。

若钩子会在外部负载均衡器中禁用当前服务器，并配合 `--rolling` 标志，可实现代理的零停机重启。

滚动重启时，该钩子会对每台服务器调用一次，`KAMAL_HOSTS` 包含当前服务器。非滚动重启时只调用一次。

使用 [post-proxy-reboot](../post-proxy-reboot) 钩子重新启用服务器。
