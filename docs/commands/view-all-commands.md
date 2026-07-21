---
title: 查看全部命令
---

# 查看全部命令

运行 `kamal --help` 可查看所有命令。

```bash
$ kamal --help
Commands:
  kamal accessory           # 管理附属服务（db/redis/search）
  kamal app                 # 管理应用
  kamal audit               # 显示服务器上的审计日志
  kamal build               # 构建应用镜像
  kamal config              # 显示合并后的配置（含密钥！）
  kamal deploy              # 将应用部署到服务器
  kamal details             # 显示所有容器的详细信息
  kamal docs [SECTION]      # 显示 Kamal 配置文档
  kamal help [COMMAND]      # 说明可用命令或某个具体命令
  kamal init                # 在 config/deploy.yml 创建配置模板，在 .kamal 创建密钥模板
  kamal lock                # 管理部署锁
  kamal proxy               # 管理 kamal-proxy
  kamal prune               # 清理旧的应用镜像和容器
  kamal redeploy            # 部署应用（不初始化服务器、不启动 kamal-proxy、不清理）
  kamal registry            # 登录/登出镜像仓库
  kamal remove              # 从服务器移除 kamal-proxy、应用、附属服务和仓库会话
  kamal rollback [VERSION]  # 将应用回滚到 VERSION
  kamal secrets             # 提取密钥的辅助命令
  kamal server              # 用 curl 和 Docker 初始化服务器
  kamal setup               # 设置全部附属服务、推送环境并部署应用
  kamal upgrade             # 从 Kamal 1.x 升级到 2.0
  kamal version             # 显示 Kamal 版本

Options:
  -v, [--verbose], [--no-verbose], [--skip-verbose]        # 详细日志
  -q, [--quiet], [--no-quiet], [--skip-quiet]              # 精简日志
      [--version=VERSION]                                  # 针对特定应用版本运行命令
  -p, [--primary], [--no-primary], [--skip-primary]        # 只在主主机上运行，而非全部
  -h, [--hosts=HOSTS]                                      # 只在这些主机上运行（逗号分隔，支持 * 通配符）
  -r, [--roles=ROLES]                                      # 只在这些角色上运行（逗号分隔，支持 * 通配符）
  -c, [--config-file=CONFIG_FILE]                          # 配置文件路径
                                                           # 默认：config/deploy.yml
  -d, [--destination=DESTINATION]                          # 指定目标环境配置（staging -> deploy.staging.yml）
  -H, [--skip-hooks]                                       # 不运行钩子
                                                           # 默认：false
      [--lock-wait], [--no-lock-wait], [--skip-lock-wait]  # 部署锁已被占用时等待，而不是立即失败
                                                           # 默认：false
      [--lock-wait-timeout=N]                              # 启用 --lock-wait 时等待部署锁的最长时间（秒）
                                                           # 默认：900
      [--lock-wait-interval=N]                             # 启用 --lock-wait 时轮询部署锁的间隔（秒）
                                                           # 默认：15
```
