---
title: Proxy
---

# kamal proxy

Kamal 使用 [kamal-proxy](https://github.com/basecamp/kamal-proxy) 将请求代理到应用容器，从而实现零停机部署。

```bash
$ kamal proxy
Commands:
  kamal proxy boot                         # 在服务器上启动代理
  kamal proxy boot_config <set|get|reset>  # 管理 kamal-proxy 启动配置
  kamal proxy details                      # 显示服务器上代理容器的详情
  kamal proxy help [COMMAND]               # 说明子命令或某个具体子命令
  kamal proxy logs                         # 显示服务器上代理的日志
  kamal proxy reboot                       # 重启服务器上的代理（停止容器、删除容器、启动新容器）
  kamal proxy remove                       # 从服务器移除代理容器和镜像
  kamal proxy restart                      # 重启服务器上已有的代理容器
  kamal proxy start                        # 启动服务器上已有的代理容器
  kamal proxy stop                         # 停止服务器上已有的代理容器
```

要升级 kamal-proxy 时，可调用 `kamal proxy reboot`。这会在每台服务器上造成短暂停机，并会提示确认。

可以使用滚动重启 `kamal proxy reboot --rolling`，避免在所有服务器上同时重启。

也可以使用 [pre-proxy-reboot](../../hooks/pre-proxy-reboot) 和 [post-proxy-reboot](../../hooks/post-proxy-reboot) 钩子，在重启时从上游负载均衡器移除并重新添加服务器。

## 启动配置

可用 `kamal proxy boot_config` 管理 kamal-proxy 的启动配置。

**注意：** 使用 `kamal proxy boot_config` 已弃用。应改用[代理运行配置](../../configuration/proxy#run-configuration)。

```bash
$ kamal proxy boot_config
Commands:
  kamal proxy boot_config set [OPTIONS]
  kamal proxy boot_config get
  kamal proxy boot_config reset

Options for 'set':
      [--publish], [--no-publish], [--skip-publish]   # 在主机上发布代理端口
                                                      # 默认：true
      [--http-port=N]                                 # 在主机上发布的 HTTP 端口
                                                      # 默认：80
      [--https-port=N]                                # 在主机上发布的 HTTPS 端口
                                                      # 默认：443
      [--log-max-size=LOG_MAX_SIZE]                   # 代理日志最大大小
                                                      # 默认：10m
      [--docker-options=option=value option2=value2]  # 传给代理容器的 Docker 选项
```

设置后，配置会保存在代理运行的那台服务器上。

若在单台服务器上运行多个应用，代理只有一个，启动配置是共享的，因此需要集中管理。

在调用 `kamal proxy boot` 或 `kamal proxy reboot` 时，会在启动时加载该配置。
