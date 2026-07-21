---
title: Lock
---

# kamal lock

管理部署锁。

不安全并发运行的命令会在执行期间获取锁。锁是主服务器上 `.kamal` 目录中原子创建的目录。

你可以直接管理它们——例如清除失败命令留下的锁，或在维护窗口期间阻止部署。

```bash
$ kamal lock
Commands:
  kamal lock acquire -m, --message=MESSAGE  # 获取部署锁
  kamal lock help [COMMAND]                 # 说明子命令或某个具体子命令
  kamal lock release                        # 释放部署锁
  kamal lock status                         # 报告锁状态
```

示例：

```bash
$ kamal lock status
  INFO [f085f083] Running /usr/bin/env mkdir -p .kamal on server1
  INFO [f085f083] Finished in 0.146 seconds with exit status 0 (successful).
There is no deploy lock
$ kamal lock acquire -m "Maintenance in progress"
  INFO [d9f63437] Running /usr/bin/env mkdir -p .kamal on server1
  INFO [d9f63437] Finished in 0.138 seconds with exit status 0 (successful).
Acquired the deploy lock
$ kamal lock status
  INFO [9315755d] Running /usr/bin/env mkdir -p .kamal on server1
  INFO [9315755d] Finished in 0.130 seconds with exit status 0 (successful).
Locked by: Deployer at 2024-04-05T08:32:46Z
Version: 75bf6fa40b975cbd8aec05abf7164e0982f185ac
Message: Maintenance in progress
$ kamal lock release
  INFO [7d5718a8] Running /usr/bin/env mkdir -p .kamal on server1
  INFO [7d5718a8] Finished in 0.137 seconds with exit status 0 (successful).
Released the deploy lock
$ kamal lock status
  INFO [f5900cc8] Running /usr/bin/env mkdir -p .kamal on server1
  INFO [f5900cc8] Finished in 0.132 seconds with exit status 0 (successful).
There is no deploy lock
```

## [等待锁](#waiting-for-the-lock)

会在运行期间自动获取锁的命令（例如 `kamal deploy`），若锁已被占用会立即失败。传入 `--lock-wait` 可改为轮询并等待锁释放：

```bash
$ kamal deploy --lock-wait
```

`--lock-wait` 只会等待其他命令运行时自动获取的锁。用 `kamal lock acquire` 手动设置的锁不会被等待，命令会立即失败并提示 "Deploy lock held manually, not waiting"。

可以修改默认超时与轮询间隔：

- `--lock-wait-timeout` — 放弃前的最长等待秒数（默认 `900`）。
- `--lock-wait-interval` — 轮询间隔秒数（默认 `15`）。

```bash
$ kamal deploy --lock-wait --lock-wait-timeout 300 --lock-wait-interval 10
```
