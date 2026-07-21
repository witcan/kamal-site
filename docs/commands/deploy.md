---
title: Deploy
---

# kamal deploy

构建应用并部署到所有服务器。默认会构建当前检出的应用版本。

Kamal 会使用 [kamal-proxy](https://github.com/basecamp/kamal-proxy) 在不停机的情况下，将请求从旧版本无缝切到新版本。

部署流程为：

1. 在本地和所有服务器上登录 Docker 镜像仓库。
2. 构建应用镜像，推送到仓库，再拉取到服务器。
3. 确保 kamal-proxy 已运行，并在 80、443 端口接受流量。
4. 用与当前 Git 版本哈希匹配的应用版本启动新容器。
5. 当新容器对 80 端口的 `GET /up` 返回 `200 OK` 后，让 kamal-proxy 将流量路由到新容器。
6. 停止运行旧版本应用的旧容器。
7. 清理未使用的镜像和已停止的容器，避免服务器磁盘被占满。

```bash
Usage:
  kamal deploy

Options:
  -P, [--skip-push]                                  # 跳过镜像构建与推送
                                                     # 默认：false
  -v, [--verbose], [--no-verbose], [--skip-verbose]  # 详细日志
  -q, [--quiet], [--no-quiet], [--skip-quiet]        # 精简日志
      [--version=VERSION]                            # 针对特定应用版本运行命令
  -p, [--primary], [--no-primary], [--skip-primary]  # 只在主主机上运行，而非全部
  -h, [--hosts=HOSTS]                                # 只在这些主机上运行（逗号分隔，支持 * 通配符）
  -r, [--roles=ROLES]                                # 只在这些角色上运行（逗号分隔，支持 * 通配符）
  -c, [--config-file=CONFIG_FILE]                    # 配置文件路径
                                                     # 默认：config/deploy.yml
  -d, [--destination=DESTINATION]                    # 指定目标环境配置（staging -> deploy.staging.yml）
  -H, [--skip-hooks]                                 # 不运行钩子
                                                     # 默认：false
```
