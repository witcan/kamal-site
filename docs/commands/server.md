---
title: Server
---

# kamal server

```bash
$ kamal server
Commands:
  kamal server bootstrap       # 配置 Docker 以运行 Kamal 应用
  kamal server exec            # 在服务器上运行自定义命令（--help 查看选项）
  kamal server help [COMMAND]  # 说明子命令或某个具体子命令
```

## [初始化服务器](#bootstrap-server)

可运行 `kamal server bootstrap` 在主机上配置 Docker。

它会检查是否已安装 Docker；若未安装，会尝试通过 [get.docker.com](https://get.docker.com/) 安装。

```bash
$ kamal server bootstrap
```

## [在所有服务器上执行命令](#execute-command-on-all-servers)

在所有服务器上运行自定义命令。

```bash
$ kamal server exec "date"
Running 'date' on 867.53.0.9...
  INFO [e79c62bb] Running /usr/bin/env date on 867.53.0.9
  INFO [e79c62bb] Finished in 0.247 seconds with exit status 0 (successful).
App Host: 867.53.0.9
Thu Jun 13 08:06:19 AM UTC 2024
```

## [在主服务器上执行命令](#execute-command-on-primary-server)

在主服务器上运行自定义命令。

```bash
$ kamal server exec --primary "date"
Running 'date' on 867.53.0.9...
  INFO [8bbeb21a] Running /usr/bin/env date on 867.53.0.9
  INFO [8bbeb21a] Finished in 0.265 seconds with exit status 0 (successful).
App Host: 867.53.0.9
Thu Jun 13 08:07:09 AM UTC 2024
```

## [在服务器上执行交互式命令](#execute-interactive-command-on-server)

在服务器上运行交互式命令。

```bash
$ kamal server exec --interactive "/bin/bash"
Running '/bin/bash' on 867.53.0.9 interactively...
root@server:~#
```
