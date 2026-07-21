---
title: 钩子概览
---

# 钩子概览

你可以在特定时机通过钩子运行自定义脚本。

钩子应放在 **.kamal/hooks** 文件夹中。运行 `kamal init` 会创建该文件夹并加入一些示例脚本。

可在配置文件中设置 `hooks_path` 来更改其位置。

若脚本以非零退出码返回，命令将被中止。

钩子命令可使用 `KAMAL_*` 环境变量，便于细粒度审计上报，例如触发部署报告或发送 JSON webhook。这些变量包括：

- `KAMAL_RECORDED_AT` — UTC 时间戳，ISO 8601 格式，例如 `2023-04-14T17:07:31Z`
- `KAMAL_PERFORMER` — 执行命令的本地用户（来自 `whoami`）
- `KAMAL_SERVICE` — 服务名称，例如 app
- `KAMAL_SERVICE_VERSION` — 用于消息的简短服务与版本，例如 app@150b24f
- `KAMAL_VERSION` — 正在部署的完整版本
- `KAMAL_HOSTS` — 命令目标主机的逗号分隔列表
- `KAMAL_COMMAND` — 正在运行的命令
- `KAMAL_SUBCOMMAND` — _可选：_ 正在运行的子命令
- `KAMAL_DESTINATION` — _可选：_ 目标环境，例如 "staging"
- `KAMAL_ROLE` — _可选：_ 目标角色，例如 "web"

可用的钩子有：

- [docker-setup](../docker-setup)
- [pre-connect](../pre-connect)
- [pre-build](../pre-build)
- [pre-deploy](../pre-deploy)
- [post-deploy](../post-deploy)
- [pre-app-boot](../pre-app-boot)
- [post-app-boot](../post-app-boot)
- [pre-proxy-reboot](../pre-proxy-reboot)
- [post-proxy-reboot](../post-proxy-reboot)

可传入 `--skip_hooks` 以跳过钩子。

**注意：** 钩子文件名必须是钩子名称，且不能有扩展名。例如，[pre-deploy](../pre-deploy) 钩子应命名为 "pre-deploy"（不要使用 .sh 或 .rb 等扩展名）。
