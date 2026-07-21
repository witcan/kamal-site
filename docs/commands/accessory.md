---
title: Accessory
---

# kamal accessory

附属服务（accessories）是应用依赖的长期运行服务。部署时不会更新它们。

它们不经过代理，因此重启会有短暂停机。你可以将主机上的卷映射到容器中，以便在重启后保持数据。

运行 `kamal accessory` 可查看和管理附属服务。

```bash
$ kamal accessory
Commands:
  kamal accessory boot [NAME]           # 在主机上启动新的附属服务（NAME=all 启动全部）
  kamal accessory details [NAME]        # 显示主机上附属服务的详情（NAME=all 显示全部）
  kamal accessory exec [NAME] [CMD...]  # 在附属服务容器内执行自定义命令（--help 查看选项）
  kamal accessory help [COMMAND]        # 说明子命令或某个具体子命令
  kamal accessory logs [NAME]           # 显示主机上附属服务的日志（--help 查看选项）
  kamal accessory reboot [NAME]         # 重启主机上的附属服务（停止容器、删除容器、启动新容器；NAME=all 处理全部）
  kamal accessory remove [NAME]         # 从主机移除附属服务容器、镜像和数据目录（NAME=all 移除全部）
  kamal accessory restart [NAME]        # 重启主机上已有的附属服务容器
  kamal accessory start [NAME]          # 启动主机上已有的附属服务容器
  kamal accessory stop [NAME]           # 停止主机上已有的附属服务容器
  kamal accessory upgrade               # 将附属服务从 Kamal 1.x 升级到 2.0（在 'kamal' 网络中重启）
```

要更新附属服务，请在配置中更新镜像，然后运行 `kamal accessory reboot [NAME]`。

示例：

```bash
$ kamal accessory boot all
Running the pre-connect hook...
  INFO [bd04b11b] Running /usr/bin/env .kamal/hooks/pre-connect on localhost
  INFO [bd04b11b] Finished in 0.003 seconds with exit status 0 (successful).
  INFO [681a028b] Running /usr/bin/env mkdir -p .kamal on server2
  INFO [e3495d1d] Running /usr/bin/env mkdir -p .kamal on server1
  INFO [e7c5c159] Running /usr/bin/env mkdir -p .kamal on server3
  INFO [e3495d1d] Finished in 0.170 seconds with exit status 0 (successful).
  INFO [681a028b] Finished in 0.182 seconds with exit status 0 (successful).
  INFO [e7c5c159] Finished in 0.185 seconds with exit status 0 (successful).
  INFO [83153af3] Running /usr/bin/env mkdir -p .kamal/locks on server1
  INFO [83153af3] Finished in 0.028 seconds with exit status 0 (successful).
Acquiring the deploy lock...
  INFO [416a654c] Running docker login registry:4443 -u [REDACTED] -p [REDACTED] on server1
  INFO [3fb56559] Running docker login registry:4443 -u [REDACTED] -p [REDACTED] on server2
  INFO [3fb56559] Finished in 0.065 seconds with exit status 0 (successful).
  INFO [416a654c] Finished in 0.080 seconds with exit status 0 (successful).
  INFO [2705083f] Running docker run --name custom-busybox --detach --restart unless-stopped --log-opt max-size="10m" --env-file .kamal/env/accessories/custom-busybox.env --label service="custom-busybox" registry:4443/busybox:1.36.0 sh -c 'echo "Starting busybox..."; trap exit term; while true; do sleep 1; done' on server2
  INFO [3cb3adb6] Running docker run --name custom-busybox --detach --restart unless-stopped --log-opt max-size="10m" --env-file .kamal/env/accessories/custom-busybox.env --label service="custom-busybox" registry:4443/busybox:1.36.0 sh -c 'echo "Starting busybox..."; trap exit term; while true; do sleep 1; done' on server1
  INFO [3cb3adb6] Finished in 0.552 seconds with exit status 0 (successful).
  INFO [2705083f] Finished in 0.566 seconds with exit status 0 (successful).
Releasing the deploy lock...
```
