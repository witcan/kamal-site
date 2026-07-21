---
title: App
---

# kamal app

运行 `kamal app` 可管理正在运行的应用。

要部署新版本应用，请参见 `kamal deploy` 和 `kamal rollback`。

你可以使用 `kamal app exec` [在服务器上运行命令](../running-commands-on-servers)。

```bash
$ kamal app
Commands:
  kamal app boot              # 在服务器上启动应用（若已在运行则重启）
  kamal app containers        # 显示服务器上的应用容器
  kamal app details           # 显示应用容器的详情
  kamal app exec [CMD...]     # 在应用容器内执行自定义命令（--help 查看选项）
  kamal app help [COMMAND]    # 说明子命令或某个具体子命令
  kamal app images            # 显示服务器上的应用镜像
  kamal app live              # 将应用设为上线模式
  kamal app logs              # 显示服务器上应用的日志（--help 查看选项）
  kamal app maintenance       # 将应用设为维护模式
  kamal app remove            # 从服务器移除应用容器和镜像
  kamal app stale_containers  # 检测过期的应用容器
  kamal app start             # 启动服务器上已有的应用容器
  kamal app stop              # 停止服务器上的应用容器
  kamal app version           # 显示服务器上当前运行的应用版本
```

## [维护模式](#maintenance-mode)

运行 `kamal app maintenance` 可将应用设为维护模式。

处于维护模式时，kamal-proxy 会拦截请求并返回 503 响应。

内置了错误页的 HTML 模板。可通过 --message 选项自定义错误信息：

```shell
$ kamal app maintenance --message "Scheduled maintenance window from ..."
```

也可以通过 [`error_pages_path`](../../configuration/overview#error-pages) 配置选项提供自定义错误页。

## [上线模式](#live-mode)

运行 `kamal app live` 可将应用恢复为上线模式。
